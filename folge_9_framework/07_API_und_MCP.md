# 07_API_und_MCP.md

# API und MCP

Version: 1.0

Dieses Dokument definiert den vollständigen Aufbau der REST-API sowie des MCP-Servers.

Beide Schnittstellen greifen ausschließlich auf die zentrale Business-Logik der Anwendung zu.

Es existiert niemals doppelte Logik.

Die API und der MCP-Server unterscheiden sich lediglich im Kommunikationsprotokoll – nicht jedoch in der eigentlichen Verarbeitung.

---

# Ziel

Die Schnittstellen sollen:

- einfach erweiterbar sein
- sicher sein
- zentral aufgebaut sein
- dieselbe Business-Logik verwenden
- identische Ergebnisse liefern
- sauber dokumentiert sein

---

# Architektur

Die Anwendung besitzt drei zentrale Einstiegspunkte.

```text
index.php
api.php
mcp.php
```

Jeder Einstiegspunkt besitzt genau eine Aufgabe.

| Datei | Aufgabe |
|---------|----------|
| index.php | Weboberfläche |
| api.php | REST API |
| mcp.php | MCP Server |

Weitere öffentliche Einstiegspunkte sind nicht vorgesehen.

---

# Zentrale Einstiegspunkte

## index.php

Stellt die Weboberfläche bereit.

Aufruf über Browser.

---

## api.php

Stellt sämtliche REST-Schnittstellen bereit.

Alle externen Systeme kommunizieren ausschließlich über

```text
/api.php
```

Es existieren keine weiteren öffentlichen API-Dateien.

---

## mcp.php

Stellt den kompletten MCP-Server bereit.

Alle MCP-Clients kommunizieren ausschließlich über

```text
/mcp.php
```

Es existieren keine weiteren öffentlichen MCP-Dateien.

---

# Aufgaben von api.php

Die Datei übernimmt ausschließlich technische Aufgaben.

Sie:

- nimmt Requests entgegen
- authentifiziert den Benutzer
- prüft Berechtigungen
- validiert Parameter
- bestimmt das gewünschte Modul
- bestimmt die gewünschte Aktion
- ruft die zentrale Business-Logik auf
- erzeugt die JSON-Antwort
- schreibt Logeinträge

Sie enthält keinerlei Geschäftslogik.

---

# Aufgaben von mcp.php

Die Datei übernimmt ebenfalls ausschließlich technische Aufgaben.

Sie:

- nimmt MCP-Requests entgegen
- authentifiziert den Client
- bestimmt Tool oder Resource
- validiert Parameter
- ruft die zentrale Business-Logik auf
- erzeugt die MCP-Antwort
- schreibt Logeinträge

Auch hier befindet sich keinerlei Fachlogik.

---

# Interner Ablauf

Alle Zugriffe folgen demselben Ablauf.

```text
Client

↓

api.php
oder
mcp.php

↓

Authentifizierung

↓

Rechteprüfung

↓

Validierung

↓

Routing

↓

Business-Logik

↓

Antwort

↓

Logging
```

---

# Routing

API und MCP besitzen einen zentralen Router.

Dieser entscheidet anhand der übergebenen Parameter, welche Funktion ausgeführt werden soll.

Beispiel API

```text
/api.php?modul=agenten&aktion=starten
```

Beispiel MCP

```text
Tool:
agent_starten
```

Der Router ruft anschließend ausschließlich zentrale Funktionen auf.

---

# Business-Logik

Die komplette Business-Logik befindet sich ausschließlich im

```text
/inc
```

Ordner.

Beispiele

```text
/inc/

funktionen_agenten.php

funktionen_benutzer.php

funktionen_modelle.php

funktionen_logs.php

funktionen_system.php
```

API und MCP greifen ausschließlich auf diese Funktionen zu.

---

# Keine doppelte Logik

Nicht erlaubt

```text
API

↓

eigene SQL-Abfragen
```

und zusätzlich

```text
MCP

↓

eigene SQL-Abfragen
```

Stattdessen

```text
API

↓

benutzer_laden()

↓

Datenbank
```

und

```text
MCP

↓

benutzer_laden()

↓

Datenbank
```

Die Funktion existiert genau einmal.

---

# Authentifizierung

Jeder Request wird authentifiziert.

Mögliche Verfahren

- Session
- API-Key
- Bearer Token
- MCP Authentication

Nicht authentifizierte Requests werden sofort beendet.

---

# Rechteprüfung

Vor jeder Aktion wird geprüft

- Benutzer vorhanden
- Benutzer aktiv
- Benutzer angemeldet
- erforderliche Berechtigung vorhanden

Erst danach erfolgt die eigentliche Verarbeitung.

---

# Validierung

Alle Eingaben werden geprüft.

Beispiele

- Datentyp
- Pflichtfelder
- Länge
- erlaubte Werte
- Dateiformate

Ungültige Eingaben werden niemals verarbeitet.

---

# Datenbankzugriffe

API und MCP greifen niemals direkt auf die Datenbank zu.

SQL befindet sich ausschließlich innerhalb der zentralen Funktionen.

Nicht erlaubt

```php
mysqli_query()

PDO->query()

INSERT

UPDATE

DELETE
```

innerhalb von

```text
api.php

mcp.php
```

---

# Antwortformat REST API

Alle Antworten besitzen denselben Aufbau.

```json
{
    "erfolg": true,
    "meldung": "",
    "daten": {}
}
```

Fehler

```json
{
    "erfolg": false,
    "meldung": "Beschreibung",
    "daten": null
}
```

---

# Antwortformat MCP

MCP verwendet das offizielle MCP-Protokoll.

Alle Antworten werden zentral erzeugt.

Tools und Resources verwenden dabei dieselbe Business-Logik wie die REST-API.

---

# Logging

Jeder Request wird protokolliert.

Gespeichert werden beispielsweise

- Benutzer
- Zeitpunkt
- Modul
- Aktion
- Antwortstatus
- Fehler
- Laufzeit

---

# Fehlerbehandlung

Technische Fehler werden niemals direkt ausgegeben.

Der Client erhält verständliche Fehlermeldungen.

Technische Details werden ausschließlich protokolliert.

---

# Erweiterbarkeit

Neue Funktionen entstehen immer nach demselben Ablauf.

1.

Neue Funktion erstellen

```text
funktionen_*.php
```

↓

2.

Routing ergänzen

↓

3.

Optional REST-Aufruf ergänzen

↓

4.

Optional MCP-Tool ergänzen

Mehr ist nicht erforderlich.

---

# Vorteile der Architektur

Diese Architektur bietet:

- genau zwei öffentliche Schnittstellen
- zentrale Authentifizierung
- zentrale Rechteprüfung
- zentrale Validierung
- zentrale Fehlerbehandlung
- zentrales Logging
- keine doppelte Business-Logik
- einfache Erweiterbarkeit
- einheitliches Verhalten aller Clients

---

# Verboten

Nicht erlaubt sind

- mehrere öffentliche API-Endpunkte
- mehrere öffentliche MCP-Endpunkte
- SQL innerhalb von api.php
- SQL innerhalb von mcp.php
- doppelte Business-Logik
- ungeprüfte Benutzereingaben
- fehlende Rechteprüfung
- unterschiedliche Implementierungen derselben Funktion

---

# Ziel

Die gesamte Anwendung besitzt genau drei Einstiegspunkte.

```text
index.php
api.php
mcp.php
```

Alle drei greifen auf dieselbe zentrale Business-Logik zu.

Dadurch entsteht eine saubere, wartbare und langfristig erweiterbare Architektur, die sowohl für klassische Webanwendungen als auch für KI-Agenten und externe Systeme optimal geeignet ist.