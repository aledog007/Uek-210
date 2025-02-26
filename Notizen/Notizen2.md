# Fragen & Antworten zur Counter-App (OpenShift/Kubernetes)

Dieses Dokument enthält alle bisher angesprochenen Fragen und deren Antworten rund um die **Counter-App** (bestehend aus Datenbank, Backend und Frontend) in einer OpenShift- bzw. Kubernetes-Umgebung.

---

## Frage 1: NetworkError im Frontend

**Frage**  
> Beim Zugriff auf das Frontend erscheint folgende Fehlermeldung im Browser:  
> „NetworkError when attempting to fetch resource“  
> Der Network-Tab zeigt, dass das Frontend `localhost:3000/api/useCases` aufruft.

**Antwort**  
1. **Wie gehe ich vor, um den Fehler zu finden?**  
   - Logs des Frontend-Pods ansehen: `oc logs -f <frontend-pod>`.  
   - Prüfen, ob `BACKEND_URL` korrekt gesetzt ist.  
   - Services/Routes checken (gibt es eine Route fürs Backend?).

2. **Worin könnte der Fehler liegen?**  
   - Das Frontend ruft fälschlicherweise `localhost:3000` auf, anstatt die echte Route des Backends (z. B. `https://counter-backend-<NAMESPACE>.apps...`).  
   - Oder Port Mismatch.

3. **Wie kann der Fehler behoben werden?**  
   - **Korrigiere `BACKEND_URL`** in der Frontend-Deployment-Konfiguration (oder in einer Frontend-ConfigMap).  
   - Den Container/Pod neu starten, damit er die korrekten Variablen lädt.

---

## Frage 2: CrashLoopBackOff im Frontend-Pod

**Frage**  
> Das Frontend ist in `CrashLoopBackOff`. Die OpenShift-Webkonsole zeigt an, dass die Container immer wieder neustarten.

**Antwort**  
1. **Vorgehen zur Fehlersuche**  
   - `oc logs <frontend-pod>` (oder `oc describe pod <frontend-pod>`) → mögliche Fehlermeldungen sehen.  
   - Prüfen, ob das Startskript richtig ist (z. B. `npm start`).  
   - Environment-Variablen kontrollieren (z. B. fehlt `BACKEND_URL`, `npm_config_cache`?).

2. **Möglicher Fehler**  
   - Falsches Startskript (`npm run build` anstelle von `npm start` o. ä.).  
   - Port stimmt nicht überein (App lauscht auf 3000, Service auf 80).  
   - ENV-Variable nicht vorhanden oder leer.

3. **Behebung**  
   - Im Dockerfile bzw. Deployment die Ports und Startbefehle abgleichen.  
   - ENV-Variablen korrekt setzen.  
   - Danach Pod/Deployment neu ausrollen.

---

## Frage 3: Welche Ressource verwaltet die Backend-Pods?

**Frage**  
> *„Wählen Sie die Ressource aus, welche die Pods des Backends verwaltet.“*

**Antwort**  
- **Backend Deployment**  
- Begründung: In Kubernetes/OpenShift erzeugt und verwaltet ein Deployment die Pods (ReplicaSets, Rolling Updates etc.).

---

## Frage 4: Welche Ressource nimmt HTTP-Requests von außen an und leitet sie ans Frontend?

**Frage**  
> *„Wählen Sie die Ressource aus, welche die HTTP Requests von außerhalb entgegennimmt und ans Frontend weitergibt.“*

**Antwort**  
- **Frontend Route** (OpenShift).  
- Hintergrund: Die Route exponiert den Service nach außen, sodass User über den Hostnamen zugreifen können.

---

## Frage 5: Welche Ressource nimmt HTTP-Requests von außen an und leitet sie ans Backend?

**Frage**  
> *„Wählen Sie die Ressource aus, welche die HTTP Requests von außerhalb entgegennimmt und im Cluster ans Backend weitergibt.“*

**Antwort**  
- **Backend Route**  
- Auch hier fungiert die Route als Entry Point fürs Backend.

---

## Frage 6: Wo liegen die Datenbank-Credentials?

**Frage**  
> *„Wählen Sie die Ressource aus, welche die Credentials für den DB-Zugriff enthält?“*

**Antwort**  
- **Database Secret**  
- Typischerweise mit Key/Value (z. B. `database-user`, `database-password`) in Base64.

---

## Frage 7: Auf welchem Port lauscht das Backend im Container?

**Frage**  
> „Das Backend kann von außen via HTTP angesprochen werden. Auf welchem Port kommen die HTTP-Anfragen im Container an?“

**Antwort**  
- **Port 8080**  
- Im Counter-Backend-Projekt ist standardmäßig `8080` konfiguriert (siehe ContainerPort/Deployment).

---

## Frage 8: Reihenfolge der Tiers (Browser → Frontend → Backend → Datenbank)

**Frage**  
> *„Bringen Sie die Tiers in die Reihenfolge, in welcher der HTTP-Request ankommt: Browser, Frontend, Backend, Datenbank.“*

**Antwort**  
1. **Browser** (User öffnet die Anwendung)  
2. **Frontend** (React)  
3. **Backend** (NodeJS)  
4. **Datenbank** (PostgreSQL)

---

## Frage 9: Programmiersprachen

**Frage**  
> „Ordnen Sie zu, welche Programmiersprache an welchem Ort eingesetzt wird (Frontend, Backend, Datenbank).“

**Antwort**  
- **Frontend**: JavaScript (React)  
- **Backend**: JavaScript (NodeJS)  
- **Datenbank**: SQL (PostgreSQL)

---

## Frage 10: Technologien (Frontend, Backend, DB, Orchestrierung)

**Frage**  
> „Welche Technologie wird wo eingesetzt? (Frontend, Backend, Datenbank, Container-Orchestrierung)“

**Antwort**  
- **Frontend**: React  
- **Backend**: NodeJS  
- **Datenbank**: Postgres  
- **Orchestrierung**: OpenShift (Kubernetes)

---

## Frage 11: Welche Ressource enthält die Information, unter welcher externen URL das Frontend das Backend erreichen kann?

**Frage**  
> *„Wählen Sie die Ressource aus, welche die Information enthält, unter welchem externen URL das Frontend das Backend erreichen kann.“*

**Antwort**  
- Häufig: **Frontend Deployment** (dort wird z. B. `BACKEND_URL` als ENV hinterlegt).  
- Alternativ: **Frontend ConfigMap** (falls die URL dort steht).  
- In den meisten Fällen → **Frontend Deployment**.

---

## Frage 12: Welche Ressource verteilt die SQL Queries im Cluster an die Datenbank-Pods?

**Frage**  
> *„Wählen Sie die Ressource aus, welche die SQL Queries von innerhalb des Clusters an die Datenbank-Pods verteilt.“*

**Antwort**  
- **Database Service**  
- Der Service leitet Verbindungen an die dahinterliegenden DB-Pods (Deployment/StatefulSet) weiter.

---

## Frage 13: Welche Ressource verteilt die HTTP Requests innerhalb des Clusters an die Backend-Pods?

**Frage**  
> *„Wählen Sie die Ressource aus, welche die HTTP Requests innerhalb des Clusters an die Pods des Backends verteilt.“*

**Antwort**  
- **Backend Service**  
- K8s-Services führen Lastverteilung durch zu den passenden Pods.

---

## Frage 14: Wie kann man alle laufenden Pods per Commandline anzeigen?

**Frage**  
> *„Wie können mit der Commandline alle laufenden Pods angezeigt werden?“*

**Antwort**  
- **`oc get pods`** (OpenShift)  
- Oder `kubectl get pods` in reinem Kubernetes.

---

## Frage 15: Wie kann man alle Manifest-Files (YAML) aus dem aktuellen Ordner deployen?

**Frage**  
> *„Wie können mit der Commandline alle YAML-Dateien aus dem aktuellen Ordner in den Cluster übernommen werden?“*

**Antwort**  
- **`oc apply -f .`** (bzw. `kubectl apply -f .`)  
- Liest alle `.yaml`-Dateien im Ordner und wendet sie an.

---

## Frage 16: Hohe Last – welche Ressource zum Skalieren?

**Frage**  
> *„Die Applikation ist sehr erfolgreich. Wie kann man bei steigenden Nutzerzahlen schnell auf mehr Last reagieren? Welche K8s-Ressource wird eingesetzt?“*

**Antwort**  
- **HorizontalPodAutoscaler (HPA)**  
- So kann das Backend und/oder Frontend automatisch hochskaliert werden, wenn CPU/Memory eine gewisse Schwelle überschreitet.

---

**Fertig!**  

> **Hinweis**:  
> - Diese Antworten basieren auf der üblichen Struktur im Counter-App-Projekt (Datenbank = PostgreSQL, Backend = NodeJS, Frontend = React).  
> - Ports und Deployments können je nach Konfiguration leicht abweichen, aber im Standardfall lauscht das Backend auf Port 8080 und das Frontend auf Port 3000.  
> - Für OpenShift werden **Routes** verwendet, in Kubernetes ohne OpenShift würde man stattdessen **Ingress**-Objekte nutzen.

**18**
apiVersion: v1
kind: Service
metadata:
  name: counter-frontend
  labels:
    app: counter-frontend
spec:
  ports:
    - name: counter-frontend
      protocol: TCP
      port: 3000
      targetPort: 3000
  selector:
    app: counter-frontend
  sessionAffinity: None


  **17**
  apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: counter-frontend
  labels:
    app: counter-frontend
spec:
  port:
    targetPort: counter-frontend
  to:
    kind: Service
    name: counter-frontend
  tls:
    termination: edge
    insecureEdgeTerminationPolicy: Redirect

**20**
apiVersion: apps/v1
kind: Deployment
metadata:
  name: counter-backend
  labels:
    app: counter-backend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: counter-backend
  template:
    metadata:
      labels:
        app: counter-backend
    spec:
      containers:
        - name: counter-backend
          image: ghcr.io/piera2007/counter-backend:v1
          # Wichtig für NPM-Cache-Bug
          env:
            - name: npm_config_cache
              value: ./.npm-cache
            # Datenbank-Konfiguration
            - name: DB_HOST
              valueFrom:
                secretKeyRef:
                  name: counter-database
                  key: database-host
            - name: DB_NAME
              valueFrom:
                secretKeyRef:
                  name: counter-database
                  key: database-name
            - name: DB_USER
              valueFrom:
                secretKeyRef:
                  name: counter-database
                  key: database-user
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: counter-database
                  key: database-password
          ports:
            - containerPort: 8080



