# Basis-Image von Nginx laden
FROM nginx:latest

# Kopiere deine HTML-Dateien in das Verzeichnis von Nginx
COPY html /usr/share/nginx/html

# Exponiere Port 80 für den Zugriff auf die Webseite
EXPOSE 80

# Startet Nginx (wird automatisch vom Image gemacht)
CMD ["nginx", "-g", "daemon off;"]
