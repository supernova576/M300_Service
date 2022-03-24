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


<div id='Testing'/>

# Testing

Kommt noch...

<div id='Quellen'/>

# Quellenangaben

Beschreibung                       Quelle

Intro.png:                         Selbst gemacht
Netzwerkplan_Modul300.png:         Selbst gemacht 

Einzelne Teile des Codes:          https://gist.github.com/aurmil/   e346aec64c3f6b6ea17259f41e3b6ab0 
