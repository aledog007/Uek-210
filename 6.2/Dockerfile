# Basis-Image von Nginx laden
FROM nginxinc/nginx-unprivileged:latest

# Kopiere deine HTML-Dateien in das Verzeichnis von Nginx
COPY html /usr/share/nginx/html

EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]