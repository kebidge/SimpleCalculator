# Übung: Continuous Integration and Deployment

Alternative: gleiche Funktion auf dem HFT-GitLab mit GitLab-CI/CD entwickeln.

## Public GitHub Repository

Erstellen Sie ein neues GitHub Repository, das Public ist - also öffentlich verfügbar. Bitte hier besonders darauf achten, dass Sie keine "Secrets" und Konfigurationen hinzufügen.

Laden Sie das Codegerüst herunter und entpacken es in Ihrem neuen Repository.

```bash
$ git clone <URL NEUES PUBLIC REPOSITORY>
$ cd <VERZEICHNIS NEUES PUBLIC REPOSITORY>
$ unzip <DOWNLOAD VERZEICHNIS>/calc_starter_uebung.zip
...
```

Testen Sie, ob das Programm lokal funktioniert:
```bash
$ mvn assembly:assembly
$ java -jar target/calc-1.0-SNAPSHOT-jar-with-dependencies.jar &
$ curl localhost:8080/calc/sum/1/1
{"result": 2}
$ fg
CTRL-C
$ 
```

Fügen Sie die Dateien zu Ihrem Repository hinzu und committen Sie.
```bash
$ git add .
$ git commit -m 'Initial import'
```

## Anmeldung drone.io

Melden Sie sich bei https://cloud.drone.io an und aktivieren Sie Ihr neues Public Repository.
Fügen Sie Ihre Secrets für Docker Hub hinzu - erstellen Sie ggfs. zuvor einen Access Token bei Docker Hub.

Kopieren Sie das Badge zum Build-Status in Ihre README.md im neuen Repository.

## Erstellen der Pipeline

Schreiben Sie die Datei .drone.yml in Ihrem Public Repository und fügen Sie die folgenden Schritte hinzu:
- Compile und Jar-Erstellung (mvn assembly:assembly)
- Unit Tests
- Statische Code-Analyse
- Veröffentlichen auf Docker Hub

## Testen Sie das Programm lokal

Fügen Sie die Pipeline zu Git hinzu und pushen Sie.
```bash
$ git add .drone.yml
$ git commit -m 'Added Drone Pipeline'
$ git push
```

Beobachten Sie den Build auf cloud.drone.io. 

## Test des erstellen Images

Testen Sie das per Drone kompilierte, getestete und veröffentlichte Image:
```bash
$ docker run -d --rm -p 8080:8080 <DOCKER HUB IMAGE NAME>
$ curl -H "Accept: application/json" localhost:8080/calc/sum/1/2
{"result": 3}
$ 
```

## Erweitern Sie das Programm

Ändern Sie den Code um die Multiplikation zu unterstützen. Schreiben Sie entsprechende Unit-Tests. Bauen Sie ggfs. einen PMD-Fehler ein.

Fügen Sie die Änderungen zu Git hinzu, pushen Sie und beobachten den Build bei Drone.

## Testen des aktuellen Images

Stopen Sie den Container, entfernen das Image und starten es neu. Testen Sie die Multiplikation. Setzen Sie hierzu Ihren eigenen Image-Namen ein.
```bash
$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                    NAMES
f3785e5a7f34        sspeiser/calculator   "java -jar calc-1.0-…"   52 minutes ago      Up 52 minutes       0.0.0.0:8080->8080/tcp   cranky_benz
$ docker kill cranky_benz
$ docker rmi sspeiser/calculator
$ docker run -d --rm -p 8080:8080 sspeiser/calculator
$ curl -H "Accept: application/json" localhost:8080/calc/sum/1/2
{"result": 3}
$ curl -H "Accept: application/json" localhost:8080/calc/prod/11/2
{"result": 22}
```
