# Dokumentation Yanis Kläy
![image](https://github.com/supernova576/Modul-300/blob/main/Pictures/Intro.png?raw=true)
<br>
<br>
# Inahltsverzeichnis

 1. [Projektbeschreibung](#Beschreibung) 

 2. [Netzwerkplan und Sicherheitskonzept](#sicherheit)

 3. [docker-compose.yaml](#yamlhehe)

 4. [Testing](#Testing)

 5. [Quellenagaben](#Quellen)
<br>
<br>
<div id='Beschreibung'/>

# Projektbeschreibung

Ziel dieses Projekts ist es, mittels Docker-Compose eine funktionierende Umgebung zu erstellen. Alle nötigen Images sind im docker-compose.yaml-File enthalten und werden beim Ausführen des Commands automatisch geladen.

In diesem Projekt wird mittels docker-compose eine funktionierende Datenbank im Backend und Next-Cloud im Frontend verwendet. Der User kann dann unter localhost:80 Nextcloud einrichten.

<br>
<div id='sicherheit'/>

# Netzwerkplan / Sicherheitskonzept

![image](https://github.com/supernova576/Modul-300/blob/main/lb3/bilder/nw-plan.png?raw=true)

Wie hier oben im Plan zu sehen ist, sind zwei Ports auf dem Container offen:
| Port   |      Nutzen     |
|:----------|:-------------|
| 80 | Wird für den Zugriff auf das Nextcloud-GUI gebraucht. |
| 5432 | PostgreSQL-Port. Ist **NICHT** von aussen her erreichbar. Nur für Kommunikation unter Containern. |

Alle anderen Ports sind weder von aussen her, noch von intern her (Container untereindander) erreichbar. 


<br>
<div id='yamlhehe'/>

# docker-compose.yaml
In diesem Abschnitt erkläre ich, wie ich das YAML-File aufgebaut habe.

```yml
version: '3.2'
```
Die Version ist sehr wichtig. Nach mehrfachem Experimentieren und Abklären, ergab sich die Version 3.2 als die beste. Falls man die Version nicht ins File schreibt, gibt es Syntax-Fehlermeldungen beim Versuch, den Container mir docker-compose zu starten.

```yml
services:
```
Hier werden die Services definiert, ergo die Applikationen, die auf dem Container laufen werden. In unserem Fall wäre das einerseits der Service "Nextcloud" und andererseits der Service "".

```yml
 nc:
    image: nextcloud:apache
    environment:
      - POSTGRES_HOST=db
      - POSTGRES_PASSWORD=hirsch123
      - POSTGRES_DB=hehedb
      - POSTGRES_USER=yanis
    ports:
      - 80:80
    restart: always
    volumes:
      - nc_data:/var/www/html

  db:
    image: postgres:alpine
    environment:
      - POSTGRES_PASSWORD=hirsch123
      - POSTGRES_DB=hehedb
      - POSTGRES_USER=yanis
    restart: always
    volumes:
      - db_data:/var/lib/postgresql/data
    expose:
      - 5432
```
Hier sehen wir unseren beiden Services. Was welche Zeile macht, erkläre ich hier:

| Befehl   |      Nutzen     |
|:----------|:-------------|
| nc: |Das ist die Beschreibung des Services "Nextcloud" (Abgekürzt auf nc). |
| image: |Dieses Image wird vom Docker-Hub (hub.docker.com) heruntergeladen. Nach erfolgreichem Herunterladen wird dieses Image extrahiert und lokal gestartet.|
| environment: | Dies sind Argumente, die man bei der Installation mitgeben kann. Beispielsweise sehen wir hier, dass der User yanis heissen wird, und das Passwort hirsch123 ist. |
| ports:  | Hier kann man definieren, welche Ports an den Container weitergeleitet werden. Es gilt --> **port_host:port_container**  |
| restart: | Beim Starten vom Docker-Deamon, wird dieser Container automatisch mitgestartet. |
| volumes:  | Volumes, die mitgegeben werden. |
| expose: | Beschreibt, welcher Port für die INTERNE Kommunikation freigegeben wird. Für eine genauere Beschreibung hilft das Kapitel Netzwerkplan / Sicherheitskonzept. |

<br>

<div id='Testing'/>

# Testing

**Wichtig**: Der Service wird unter localhost:80 verfügbar sein, falls dieser Port bereits in Benutzung ist, bitte die Zeile **ports:** im YAML-File abändern (siehe Code unten): 

```yml
nc:
    image: nextcloud:apache
    environment:
      - POSTGRES_HOST=db
      - POSTGRES_PASSWORD=hirsch123
      - POSTGRES_DB=hehedb
      - POSTGRES_USER=yanis
    ports:
      - <Beliebiger Port>:80  <-- Hier den Port ändern
    restart: always
    volumes:
      - nc_data:/var/www/html
```
Im Ordner, indem sich das YAML-File befindet, geben wir nun folgenden Command ein:

```yml
docker-compose up -d
```
Beim ersten Ausführen des Codes geht es ein wenig länger, da Docker zuerst die Images vom Dockerhub herunterladen muss. Anschliessend sollte folgendes zu sehen sein: 

![image](https://github.com/supernova576/Modul-300/blob/main/lb3/bilder/docker-compose.png?raw=true)

Anschliessend geben wir in einem beliebigen Browser die folgende URL ein: http://localhost:80 oder http://localhost:zuvor_festgelegter_Port

Man sollte nun folgendes sehen:
![image](https://github.com/supernova576/Modul-300/blob/main/lb3/bilder/login.png?raw=true)

Nun melden wir uns mit unseren Anmelde-Daten an, die im YAML-File zuvor definiert wurden:

| Username   |      Password     |
|:----------|:-------------|
| yanis | hirsch123 |

Nun sollten wir eingeloggt sein. Das Dashboard sollte ungefähr so aussehen:
![image](https://github.com/supernova576/Modul-300/blob/main/lb3/bilder/dashboard.png?raw=true)

Viel Spass beim Benutzen von Nextcloud :)

<br>
<div id='Quellen'/>

# Quellenangaben

| Objekt   |      Quelle     |
|:----------|:-------------|
| Docker-Compose generell | https://docs.docker.com/compose/gettingstarted/ |
| Gute Erklärung zu restart: always | https://serverfault.com/questions/884759/how-does-restart-always-policy-work-in-docker-compose |
| Projektidee: |https://github.com/docker/awesome-compose|
