# Dokumentation Yanis Kläy
![image](https://github.com/supernova576/Modul-300/blob/main/Pictures/Intro.png?raw=true)


# Inahltsverzeichnis
 1. [Auftragsdefinition](#Auftragsdefinition)
 
 1. [Einführung](#Einführung) 

 2. [Vagrant Code](#Vagrant)

 3. [Testing](#Testing)

 4. [Quellenagaben](#Quellen)

<div id='Auftragsdefinition'/>

# Auftrag

Sie erstellen -auf Basis von VirtualBox/Vagrant- ein selbst gewähltes **«Infrastructure as Code»** Projekt, indem sie einen Service oder Serverdienst **automatisieren.**

Teamarbeit ist erwünscht. Die Implementation des **IaC-Projekts** erfolgt hingegen **als Einzelarbeit**. Der erstellte Code sowie die gesamte Dokumentation wird versioniert auf [GitHub](https://github.com/), hinterlegt und der Lehrpersonzugänglich gemacht (Lese-Rechte).

Das Internet ist eine wichtige Ressource für solche Projekte. Entsprechend dürfen sie auch Codebeispiele aus dem Internet verwenden, sofern sie entsprechende Quellenangaben machen.

Der verwendete Code muss von ihnen vollständigdokumentiert sein, das gilt auch für Code, mindestens in groben Zügen, welchen sie aus fremden Quellen verwenden. 

**Das bedeutet sie können über den verwendeten Code Auskunft geben.**

<div id='Einführung'/>

# Einführung

Die Projektidee entstand im Plenum. Da viele einen Webserver mit einer Datenbank machten, wollten wir etwas leicht "spezielleres" machen. Im Frontend arbeitet ein Apache2 Webserver, der die Dienste **Adminer und OPcache** in einem GUI ersichtlich machen sollte. Im Hintergrund laufen PHP und MariaDB, damit die Services ordnungsgemäss funktionieren. All dies sollte via Portweiterleitung ereichbar sein.

Um das Ganze besser verstehen zu können, hilft diese Grafik:

![image](https://github.com/supernova576/Modul-300/blob/main/Pictures/Netzwerkplan_Modul300.png?raw=true)

Auf dem Hostsystem können wir http://localhost:8080 eingeben. Anschliessend leitet dieser die Anfrage an die VM auf Port 80 weiter. Wir sollten in der Lage sein, die Services so nutzen zu können.



<div id='Vagrant'/>

# Vagrant Code

Um besser zu verstehen, welcher Teil was macht, analysieren wir hier den Code:

**Zuerst konfigurieren wir die VM selbst: Ubuntu 1804, der Portforward und Konfigurationen für die Bereitstellung des Webroots werden mit folgenden Zeilen konfiguriert**

  Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu1804"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.ssh.forward_agent = true

#freigegebener Folder für die Website
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox", 

#Gruppe und owner für die Folder festlegen (Sicherheit)
    owner: "www-data", group: "www-data" 


#Virtualbox Config
  config.vm.provider "virtualbox" do |vb| 

    vb.name = "vagrant_ubuntuModul300"
    vb.memory = 1024
    vb.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"] 
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]  
  end

**Hier fängt der eigentliche Teil der Konfiguration an. Als erstes installieren wir alle Services und deren Abhängigkeiten (PHP, MariaDB, Apache2) vom Repository von Ubuntu**

#Installation of all softwares
config.vm.provision "shell" do |s| 
    s.args = [time_zone] 
    s.inline = <<-SHELL 

# set timezone
TIME_ZONE=$1
# Variable für die Installation setzen
export DEBIAN_FRONTEND=noninteractive 
# Zeitzone updaten
timedatectl set-timezone "$TIME_ZONE"
# Update all packages
apt-get update -q
# install vim and git to write into the /var/www-Folder
apt-get install -q -y vim git
# install Apache, PHP and MySQL
apt-get install -q -y apache2
apt-get install -q -y php7.2 libapache2-mod-php7.2
apt-get install -q -y php7.2-curl php7.2-gd php7.2-mbstring php7.2-mysql php7.2-xml php7.2-zip php7.2-bz2 php7.2-intl
apt-get install -q -y mariadb-server mariadb-client
a2enmod rewrite headers
systemctl restart apache2

**Nun werden die Installationsparameter für die Frontend-Services definiert**

# set Vagrant folder as Apache root folder and go to it
dir='/vagrant/www'
if [ ! -d "$dir" ]; then
  mkdir "$dir"
fi
if [ ! -L /var/www/html ]; then
  rm -rf /var/www/html
  ln -fs "$dir" /var/www/html
fi
cd "$dir"
# Vhosts konfigurieren
file='/etc/apache2/sites-available/dev.conf'
if [ ! -f "$file" ]; then
  SITE_CONF=$(cat <<EOF
<Directory /var/www/html>
  AllowOverride All
  Options +Indexes -MultiViews +FollowSymLinks
  AddDefaultCharset utf-8
  SetEnv ENVIRONMENT "development"
  php_flag display_errors On
  EnableSendfile Off
</Directory>
EOF
)
  echo "$SITE_CONF" > "$file"
fi
a2ensite dev
systemctl reload apache2

**Am Schluss werden die Front-End Services installiert.**

# Composer
EXPECTED_SIGNATURE="$(wget -q -O - https://composer.github.io/installer.sig)"
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
ACTUAL_SIGNATURE="$(php -r "echo hash_file('SHA384', 'composer-setup.php');")"
php composer-setup.php --quiet
rm composer-setup.php
mv composer.phar /usr/local/bin/composer
chmod +x /usr/local/bin/composer
sudo -H -u vagrant bash -c 'composer global require hirak/prestissimo'


# OPcache gui script
file='opcache.php'
if [ ! -f "$file" ]; then
  wget -nv -O "$file" https://raw.githubusercontent.com/amnuts/opcache-gui/master/index.php
fi
# Adminer script
file='adminer.php'
if [ ! -f "$file" ]; then
  wget -nv -O "$file" http://www.adminer.org/latest.php
  wget -nv https://raw.githubusercontent.com/vrana/adminer/master/designs/pepa-linha/adminer.css
fi

**Hier wird der Account für das Web-Login erstellt. Später auf der Website sollte man sich mit dem Usenamen Yanis und dem Passwort Yanis12345! anmelden können.**

#Login für GUI erstellen 
sudo mysql <<-EOF

  create user 'yanis'@'localhost' identified by 'Yanis12345!';
  Grant all privileges on *.* to 'yanis'@'localhost';
  Flush privileges;

EOF


SHELL
  end
end

**Hier ist das Script zu Ende**

<div id='Testing'/>

# Testing

Hier sieht man den Index von http://localhost:8080 

![image](https://github.com/supernova576/Modul-300/blob/main/Pictures/Index.png?raw=true)

Gelb umrandet sieht man die zwei Services, die vom Server bereitgestellt werden. Gehen wir nun auf beide Services etwas genauer ein:

## Adminer
Nachdem man sich erfolgreich auf dem GUI mit den gegebenen Credentials angemeldet hat, ist man in der Lage seine Datenbank durch Adminer zu verwalten. Sehr praktisch und nützlich für DB-Admins.

![image](https://github.com/supernova576/Modul-300/blob/main/Pictures/adminer.png?raw=true)

## OPcache
OPcache ist primär für die Systemüberwachung konzipiert worden. Zu sehen sind nützliche und wichtige Informationen zusammengetragen auf einer Seite.

![image](https://github.com/supernova576/Modul-300/blob/main/Pictures/opcache.png?raw=true)

<div id='Quellen'/>

# Quellenangaben

Beschreibung      |                 Quelle

Intro.png: |                       Selbst gemacht
Netzwerkplan_Modul300.png: |       Selbst gemacht 

Einzelne Teile des Codes: |        https://gist.github.com/aurmil/   e346aec64c3f6b6ea17259f41e3b6ab0 
