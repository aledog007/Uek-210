### Was ist Redis?

Stell dir vor, Redis ist wie ein sehr schnelles Notizbuch, das du immer bei dir hast. Wenn du etwas schnell aufschreiben und später wiederfinden möchtest, schreibst du es in dieses Notizbuch. Zum Beispiel:

* Du schreibst auf: "Lieblingsfarbe von Anna ist Blau".
* Wenn du später wissen möchtest, was Annas Lieblingsfarbe ist, schaust du einfach in dein Notizbuch und findest die Antwort sofort.

Redis funktioniert ähnlich, aber es speichert diese Informationen in einem Computer, damit Programme sie schnell abrufen können.

### Welche Ports werden genutzt?

In dem bereitgestellten compose.yaml-Ausschnitt wird der Port 8000 auf dem Host mit dem Port 5000 im Container verbunden:

```yaml
ports:
  - "8000:5000"
```

### Was ist die Bedeutung von ENV im DOCKERFILE?

Das `ENV`-Schlüsselwort im Dockerfile wird verwendet, um Umgebungsvariablen innerhalb des Containers zu setzen. Diese Variablen können zur Konfiguration der Anwendung oder zur Bereitstellung von Werten verwendet werden, die während der Laufzeit benötigt werden. Hier ist ein Beispiel:

```dockerfile
ENV DATABASE_URL=postgres://user:password@hostname:5432/dbname
```

In diesem Beispiel wird die Umgebungsvariable `DATABASE_URL` gesetzt, die von der Anwendung verwendet werden kann, um eine Verbindung zur Datenbank herzustellen.

ENV steht für Enviroment
