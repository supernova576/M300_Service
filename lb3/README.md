# Dokumentation Yanis Kläy
![image](https://github.com/supernova576/Modul-300/blob/main/Pictures/Intro.png?raw=true)


# Inahltsverzeichnis

 1. [Projektbeschreibung](#Beschreibung) 

 2. [docker-compose.yaml](#yamlhehe)

 3. [Testing](#Testing)

 4. [Quellenagaben](#Quellen)

<div id='Beschreibung'/>

# Projektbeschreibung

Ziel dieses Projekts ist es, mittels Docker-Compose eine funktionierende Umgebung zu erstellen. Alle nötigen Images sind im docker-compose.yaml-File enthalten und werden beim Ausführen des Commands automatisch geladen.

In diesem Projekt wird mittels docker-compose eine funktionierende Datenbank im Backend und Next-Cloud im Frontend verwendet. Der User kann dann unter localhost:80 Nextcloud einrichten.

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
```
Hier sehen wir unseren ersten kompletten Service. Was welche Zeile macht, erkläre ich hier:

| Befehl   |      Nutzen     |
|:----------|:-------------|
| nc: |Das ist die Beschreibung des Services "Nextcloud" (Abgekürzt auf nc). |
| image: |Dieses Image wird vom Docker-Hub (hub.docker.com) heruntergeladen. Nach erfolgreichem Herunterladen wird dieses Image extrahiert und lokal gestartet.|
| environment: | Dies sind Argumente, die man bei der Installation mitgeben kann. Beispielsweise sehen wir hier, dass der User yanis heissen wird, und das Passwort hirsch123 ist. |
| ports:  | Hier kann man definieren, welche Ports an den Container weitergeleitet werden. Es gilt --> **port_host:port_container**  |
| restart: | Beim Starten vom Docker-Deamon, wird dieser Container automatisch mitgestartet. |
| volumes:  | Volumes, die mitgegeben werden. |


<div id='Testing'/>

# Testing

--- kommt bald ---

<div id='Quellen'/>

# Quellenangaben

| Objekt   |      Quelle     |
|:----------|:-------------|
| Docker-Compose generell | https://docs.docker.com/compose/gettingstarted/ |
| Gute Erklärung zu restart: always | https://serverfault.com/questions/884759/how-does-restart-always-policy-work-in-docker-compose |
|  |  |
|  |  |
