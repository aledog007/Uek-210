Wie erstelle ich ein Docker build also baue ich ein image

ich erstelle ein ordner für das Projekt und gehe Hinein

```
mkdir C:\Users\DeinBenutzername\my-webserver
cd C:\Users\DeinBenutzername\my-webserver
```

erstelle ein verzeichnis für das HTML und eine datei mit dem Kontext unten folgend

```
mkdir html
echo "<h1>Hello, Docker on Windows!</h1>" > html/index.html
```

Erstelle eine Datei namens `Dockerfile` im Verzeichnis `my-webserver` und füge den folgenden Inhalt ein:

```
# Basis-Image von Nginx laden
FROM nginx:latest

# Kopiere deine HTML-Dateien in das Verzeichnis von Nginx
COPY html /usr/share/nginx/html

# Exponiere Port 80 für den Zugriff auf die Webseite
EXPOSE 80

# Startet Nginx (wird automatisch vom Image gemacht)
CMD ["nginx", "-g", "daemon off;"]
```

Gehe ins verzeichnis vom Projekt und build es (image)

```
docker build -t my-nginx .
```

starte das Projekt/image bzw. Webserver in einem container

```
docker run -d -p 8080:80 --name webserver my-nginx

-d: detech also man kann kein Terminal verwenden (im Hintergrund laufen lassen)

-p: für Portmaping (weiterleitungen der Ports)

--name: Name des Containers 

am schluss image name in diesem beispiel: my-nginx
```

besuche die Seite

```
http://localhost:8080
```

Um das Docker container zu stopen

```
docker stop webserver
```
