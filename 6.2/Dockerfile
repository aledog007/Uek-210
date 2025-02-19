# Basis-Image von Nginx laden
FROM nginxinc/nginx-unprivileged:latest

# Kopiere deine HTML-Dateien in das Verzeichnis von Nginx
COPY html /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]