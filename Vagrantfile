time_zone = "Europe/Zurich"

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




### Custom Code ###

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

#Login für GUI erstellen 
sudo mysql <<-EOF

  create user 'yanis'@'localhost' identified by 'Yanis12345!';
  Grant all privileges on *.* to 'yanis'@'localhost';
  Flush privileges;

EOF


SHELL
  end
end
