# 17_Agentenverwaltung_und_Client.md

# Agentenverwaltung, Agenten-API und Noob2Claw-Client

Version: 1.0  
Bereich: Folge 10  
Zielsystem: Noob2Claw Core + externe Agenten-Clients  
Technologien: PHP, MariaDB, Apache, OpenClaw, Hermes, Codex, Grok, Claude CLI

---

# Ziel

In Folge 9 wurde das Grundsystem aufgebaut:

- Webinterface
- zentrale Navigation
- Benutzerverwaltung
- Rechteverwaltung
- API
- MCP
- Logs
- Einstellungen
- Grundstruktur für Agenten

In Folge 10 wird das System um die echte Agentenverwaltung erweitert.

Das Ziel ist nicht nur eine Liste von Agenten im Webinterface.  
Das Ziel ist eine steuerbare Infrastruktur aus:

```text
Noob2Claw Core
        │
        │ REST API
        ▼
Noob2Claw Client
        │
        │ lokale Konsole
        ▼
OpenClaw / Hermes / Codex / Grok / Claude CLI
```

Der Core verwaltet Agenten, Aufträge, Konfigurationen, Logs und Updates.  
Der Client läuft auf einem Agenten-System und holt sich regelmäßig Arbeit vom Core.

---

# Grundprinzip

Der Core ist die zentrale Steuerung.

Der Client ist nur der ausführende Arbeiter.

Der Client entscheidet nicht selbst, was fachlich passieren soll.  
Er fragt den Core:

```text
Hast du Arbeit für mich?
```

Der Core antwortet mit einer Aktion.

Der Client führt die Aktion lokal aus und sendet das Ergebnis zurück.

---

# Begriffe

| Begriff | Bedeutung |
|---|---|
| Core | Das zentrale Noob2Claw-Websystem unter `/var/www/noobclaw` |
| Client | Das PHP-Script auf einem Agenten-Server unter `/home/noobclaw/noob2claw_client/` |
| Agent | Eine logische KI-Rolle, Persona oder Arbeitsinstanz |
| Agenten-Client | Ein physischer oder virtueller Server, auf dem der Client läuft |
| Auftrag | Eine vom Core erzeugte Aufgabe für einen Agenten-Client |
| Aktion | Der technische Typ eines Auftrags, zum Beispiel `chat`, `agenten_schreiben`, `hardwareinfo_abholen` |
| Prozess | Eine aktive Client-Laufzeit mit Prozess-ID und Heartbeat |
| Update | Eine neue Version des Client-Scripts, die vom Core ausgeliefert wird |

---

# Architektur

```text
Browser
  │
  ▼
Noob2Claw Webinterface
  │
  ├── Agentenverwaltung
  ├── Clientverwaltung
  ├── Auftragsübersicht
  ├── Logansicht
  └── Updateverwaltung

Noob2Claw Core
  │
  ├── index.php
  ├── api.php
  ├── mcp.php
  ├── inc/funktionen_agents.php
  ├── inc/funktionen_agenten_clients.php
  ├── inc/funktionen_agenten_auftraege.php
  ├── inc/funktionen_agenten_client_update.php
  └── noob2claw_client/
        ├── client.php
        ├── funktionen.php
        ├── config.example.php
        ├── install.sh
        └── manifest.json

Agenten-System
  │
  └── /home/noobclaw/noob2claw_client/
        ├── client.php
        ├── config.php
        ├── funktionen.php
        ├── cache/
        ├── logs/
        ├── run/
        ├── tmp/
        └── workspace/
```

---

# Verzeichnisstruktur auf dem Core

Der Client-Code liegt zusätzlich auf dem Core-System, damit Agenten-Clients ihn herunterladen oder aktualisieren können.

Pfad:

```text
/var/www/noobclaw/noob2claw_client/
```

Inhalt:

```text
noob2claw_client/
├── client.php
├── funktionen.php
├── config.example.php
├── install.sh
├── manifest.json
└── README_CLIENT.md
```

Wichtig:

- Dieser Ordner darf nicht ungeschützt öffentlich auslieferbar sein.
- Direkter Browserzugriff soll per `.htaccess` gesperrt werden.
- Downloads und Updates erfolgen über `api.php`.
- Der Core prüft vor jeder Auslieferung die Berechtigung des Clients.
- Jede Datei besitzt im Manifest eine Version und einen SHA256-Hash.

Beispiel `.htaccess`:

```apache
Require all denied
```

Der Download erfolgt ausschließlich über:

```text
/api.php?modul=client&aktion=datei_holen
```

---

# Verzeichnisstruktur auf dem Agenten-Client

Pfad:

```text
/home/noobclaw/noob2claw_client/
```

Inhalt:

```text
/home/noobclaw/noob2claw_client/
├── client.php
├── config.php
├── funktionen.php
├── cache/
├── logs/
├── run/
├── tmp/
└── workspace/
```

| Pfad | Aufgabe |
|---|---|
| `client.php` | Hauptprozess des Clients |
| `config.php` | lokale Verbindung zum Core |
| `funktionen.php` | Hilfsfunktionen des Clients |
| `cache/` | temporär geladene Dateien und Attachments |
| `logs/` | lokale Client-Logs |
| `run/` | Lockdatei, Prozessstatus, Stop-Signale |
| `tmp/` | temporäre Verarbeitung |
| `workspace/` | Arbeitsbereich für Agenten und CLI-Tools |

---

# Agentenverwaltung im Webinterface

## Ziel

Die Agentenverwaltung steuert die logischen KI-Agenten.

Ein Agent ist nicht automatisch ein Server.  
Ein Agent ist zuerst eine Rolle.

Beispiele:

```text
Claudio Architect
Claudia Developer
Rudi Tester
Viktoria AMD
Noob2Claw Main Agent
```

Ein Agent kann später einem Client, Modell oder API-Typ zugeordnet werden.

---

# Pflichtfunktionen der Agentenverwaltung

Die Agentenverwaltung muss mindestens folgende Funktionen enthalten:

- Agent anlegen
- Agent bearbeiten
- Agent deaktivieren
- Agent klonen
- Agent testen
- Agent einem Client zuordnen
- Agent einem Modell zuordnen
- API-Typ wählen
- Systemprompt / Persona pflegen
- Arbeitsbereich anzeigen
- letzte Aktivität anzeigen
- letzten Fehler anzeigen
- Aufträge anzeigen
- Logs anzeigen
- Agenten-Sync zum Client auslösen

---

# Agentenfelder

| Feld | Beschreibung |
|---|---|
| `id` | interne ID |
| `titel` | sichtbarer Name des Agenten |
| `beschreibung` | kurze Beschreibung |
| `rolle` | fachliche Rolle des Agenten |
| `systemprompt` | Persona / Arbeitsanweisung |
| `api_typ` | `openclaw_ws`, `openclaw_cli`, `hermes_cli`, `codex_cli`, `grok_cli`, `claude_cli` |
| `modell` | bevorzugtes Modell |
| `fallback_modelle_json` | alternative Modelle als JSON-Liste |
| `client_id` | bevorzugter Agenten-Client oder 0 |
| `workspace_pfad` | optionaler Arbeitsbereich |
| `aktiv` | 1 aktiv, 0 deaktiviert |
| `geloescht` | Soft Delete |
| `sortierung` | Reihenfolge im UI |
| `letzte_aktivitaet` | Unix-Timestamp |
| `letzter_fehler` | letzte Fehlermeldung |
| `erstellt_am` | Unix-Timestamp |
| `geaendert_am` | Unix-Timestamp |

---

# API-Typen

| API-Typ | Beschreibung |
|---|---|
| `openclaw_ws` | Kommunikation über laufenden OpenClaw Gateway / WebSocket |
| `openclaw_cli` | direkter OpenClaw CLI-Aufruf |
| `hermes_cli` | Hermes CLI-Aufruf |
| `codex_cli` | Codex CLI-Aufruf |
| `grok_cli` | Grok CLI-Aufruf |
| `claude_cli` | Claude CLI-Aufruf |

Wichtig:

- Der API-Typ wird im Core gepflegt.
- Der Client führt nur aus, was der Core vorgibt.
- Ist ein API-Typ lokal nicht installiert, meldet der Client einen sauberen Fehler zurück.
- Kein Client darf heimlich auf einen anderen API-Typ ausweichen, ohne dass der Core es vorgibt.

---

# Clientverwaltung im Webinterface

Die Clientverwaltung steuert die physischen oder virtuellen Agenten-Systeme.

Ein Client ist ein Server, auf dem das Script läuft.

Beispiele:

```text
noobclaw-agent-001
noobclaw-ai-003
r9700-server
codex-worker
```

---

# Pflichtfunktionen der Clientverwaltung

- Client anlegen
- Client bearbeiten
- Client deaktivieren
- API-Token erzeugen
- API-Token neu erzeugen
- Installationsbefehl anzeigen
- Client-Version anzeigen
- Update auslösen
- Heartbeat anzeigen
- Hardwareinfo anzeigen
- laufende Aufträge anzeigen
- letzte Logs anzeigen
- Client sperren
- Client löschen nur als Soft Delete

---

# Clientfelder

| Feld | Beschreibung |
|---|---|
| `id` | interne ID |
| `titel` | Name des Clients |
| `hostname` | Hostname des Agenten-Systems |
| `beschreibung` | Beschreibung |
| `token_hash` | Hash des Client-Tokens |
| `secret_key_hash` | Hash für zusätzliche Absicherung |
| `client_version` | installierte Client-Version |
| `client_manifest_hash` | Hash des letzten Update-Manifests |
| `letzter_heartbeat` | Unix-Timestamp |
| `letzte_ip` | letzte IP-Adresse |
| `status` | `offline`, `online`, `arbeitet`, `fehler`, `gesperrt` |
| `hardware_json` | CPU, RAM, GPU, Platten als JSON |
| `faehigkeiten_json` | verfügbare CLIs und Tools |
| `max_parallel` | Anzahl paralleler Aufgaben, Standard 1 |
| `aktiv` | aktiv ja/nein |
| `geloescht` | Soft Delete |
| `erstellt_am` | Unix-Timestamp |
| `geaendert_am` | Unix-Timestamp |

---

# Auftragsverwaltung

Aufträge sind die Arbeitspakete, die der Client abholt.

Ein Auftrag besitzt immer:

- Aktion
- Referenz
- Referenz-ID
- Agent-ID
- Text oder Payload
- Status
- Priorität
- Zeitstempel
- Ergebnis
- Fehlerstatus

---

# Auftragsstatus

| Status | Bedeutung |
|---|---|
| `offen` | Auftrag wartet auf Ausführung |
| `reserviert` | Auftrag wurde einem Client zugewiesen |
| `in_arbeit` | Client verarbeitet den Auftrag |
| `erledigt` | Auftrag wurde erfolgreich abgeschlossen |
| `fehler` | Auftrag ist fehlgeschlagen |
| `abgebrochen` | Auftrag wurde abgebrochen |
| `timeout` | Auftrag wurde zu lange nicht abgeschlossen |

---

# Wichtige Regel gegen Doppelverarbeitung

Ein Auftrag darf niemals gleichzeitig von zwei Clients verarbeitet werden.

Deshalb muss der Core beim Abholen eines Auftrags atomar reservieren.

Beispiel:

```sql
UPDATE agenten_auftraege
SET status = 'reserviert', client_id = :client_id, reserviert_am = :time, reserviert_bis = :timeout
WHERE id = :id
AND status = 'offen'
```

Nur wenn genau eine Zeile geändert wurde, gehört der Auftrag diesem Client.

---

# Datenbanktabellen

## Tabelle `agenten_clients`

Aufgabe:

Speichert alle ausführenden Client-Systeme.

Pflichtfelder:

```text
id
uuid
titel
hostname
beschreibung
token_hash
secret_key_hash
client_version
client_manifest_hash
letzter_heartbeat
letzte_ip
status
hardware_json
faehigkeiten_json
max_parallel
aktiv
gesperrt
geloescht
erstellt_am
geaendert_am
```

---

## Tabelle `agenten_auftraege`

Aufgabe:

Speichert alle Aufgaben, die vom Core an Clients verteilt werden.

Pflichtfelder:

```text
id
client_id
agent_id
aktion
referenz
referenz_id
prioritaet
status
payload_json
text
modell
api_typ
dateien_json
ergebnis_json
fehlermeldung
versuche
max_versuche
reserviert_am
reserviert_bis
gestartet_am
beendet_am
erstellt_am
geaendert_am
```

---

## Tabelle `agenten_client_log`

Aufgabe:

Speichert technische Logmeldungen der Clients.

Pflichtfelder:

```text
id
client_id
prozess_id
auftrag_id
level
meldung
kontext_json
erstellt_am
```

---

## Tabelle `agenten_client_prozesse`

Aufgabe:

Speichert aktive oder historische Client-Prozesse.

Pflichtfelder:

```text
id
client_id
prozess_uuid
pid
hostname
version
status
info_json
gestartet_am
letzter_heartbeat
beendet_am
beendigungsgrund
```

---

## Tabelle `agenten_client_updates`

Aufgabe:

Speichert verfügbare Client-Versionen.

Pflichtfelder:

```text
id
version
manifest_json
beschreibung
aktiv
pflichtupdate
erstellt_am
geaendert_am
```

---

# Agenten-API

Alle Client-Zugriffe erfolgen über den zentralen API-Einstiegspunkt:

```text
/var/www/noobclaw/api.php
```

Öffentlicher Aufruf:

```text
https://CORE-DOMAIN/api.php
```

Es werden keine zusätzlichen öffentlichen API-Dateien angelegt.

---

# Authentifizierung

Clients authentifizieren sich mit Bearer Token.

Header:

```http
Authorization: Bearer CLIENT_TOKEN
```

Zusätzlich sendet der Client:

```json
{
  "client_uuid": "...",
  "hostname": "...",
  "version": "..."
}
```

Regeln:

- Token wird im Core niemals im Klartext gespeichert.
- Gespeichert wird nur ein sicherer Hash.
- Der Client darf gesperrt werden können.
- Gesperrte Clients erhalten keine Aufträge.
- Jede Anfrage wird geloggt.

---

# Einheitliches Antwortformat

Jede API-Antwort nutzt dasselbe Format:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {}
}
```

Fehler:

```json
{
  "erfolg": false,
  "meldung": "Beschreibung des Fehlers",
  "daten": {}
}
```

Wichtig:

- Kein `null` verwenden.
- Leere Objekte als `{}`.
- Leere Listen als `[]`.
- Zeitangaben als Unix-Timestamp.

---

# API-Aktionen für Clients

## `client_starten`

Der Client meldet einen neuen Prozessstart.

Request:

```json
{
  "modul": "client",
  "aktion": "client_starten",
  "client_uuid": "...",
  "hostname": "...",
  "version": "1.0.0",
  "pid": 12345,
  "info_llm": "[]"
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "prozess_id": 123,
    "prozess_uuid": "...",
    "serverzeit": 1784630000,
    "max_laufzeit_sekunden": 180
  }
}
```

---

## `client_heartbeat`

Der Client meldet, dass er noch lebt.

Request:

```json
{
  "modul": "client",
  "aktion": "client_heartbeat",
  "prozess_uuid": "...",
  "arbeitet": 0,
  "auftrag_id": 0
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "beenden": 0,
    "update_verfuegbar": 0
  }
}
```

---

## `client_hole_auftrag`

Der Client fragt nach dem nächsten Auftrag.

Request:

```json
{
  "modul": "client",
  "aktion": "client_hole_auftrag",
  "prozess_uuid": "...",
  "faehigkeiten": ["openclaw_cli", "hermes_cli", "codex_cli"]
}
```

Response ohne Auftrag:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "auftrag_vorhanden": 0,
    "warten_ms": 500
  }
}
```

Response mit Auftrag:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "auftrag_vorhanden": 1,
    "auftrag_id": 456,
    "aktion": "chat",
    "agent_id": 11,
    "api_typ": "openclaw_cli",
    "modell": "",
    "referenz": "chat",
    "referenz_id": "abc123",
    "text": "Schreibe eine Antwort...",
    "payload": {},
    "dateien": []
  }
}
```

---

## `client_auftrag_start`

Der Client bestätigt, dass er die Verarbeitung begonnen hat.

Request:

```json
{
  "modul": "client",
  "aktion": "client_auftrag_start",
  "auftrag_id": 456,
  "prozess_uuid": "..."
}
```

---

## `client_ergebnis`

Der Client übermittelt das Ergebnis.

Request:

```json
{
  "modul": "client",
  "aktion": "client_ergebnis",
  "auftrag_id": 456,
  "prozess_uuid": "...",
  "ergebnis": {
    "text": "Antwort des Agenten",
    "dateien_bearbeitet": [],
    "tools_verwendet": []
  },
  "laufzeit_ms": 12345
}
```

---

## `client_fehler`

Der Client übermittelt einen Fehler.

Request:

```json
{
  "modul": "client",
  "aktion": "client_fehler",
  "auftrag_id": 456,
  "prozess_uuid": "...",
  "fehlermeldung": "OpenClaw CLI ist nicht installiert.",
  "kontext": {}
}
```

---

## `client_log`

Der Client sendet technische Logmeldungen.

Request:

```json
{
  "modul": "client",
  "aktion": "client_log",
  "prozess_uuid": "...",
  "level": "info",
  "meldung": "Auftrag gestartet",
  "kontext": {}
}
```

---

## `client_hardwareinfo_uebertragen`

Der Client sendet Hardwaredaten.

Daten:

- CPU
- RAM
- Nvidia GPU per `nvidia-smi`
- AMD GPU per `rocm-smi` oder `amd-smi`
- Festplatten per `df`
- Load Average
- verfügbare CLI-Binaries

---

## `client_update_pruefen`

Der Client fragt, ob eine neue Version verfügbar ist.

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "update_verfuegbar": 1,
    "version": "1.0.1",
    "pflichtupdate": 0,
    "manifest_hash": "...",
    "dateien": [
      {
        "dateiname": "client.php",
        "sha256": "...",
        "groesse": 12345
      }
    ]
  }
}
```

---

## `client_datei_holen`

Der Client lädt eine Update-Datei vom Core.

Request:

```json
{
  "modul": "client",
  "aktion": "client_datei_holen",
  "version": "1.0.1",
  "dateiname": "client.php"
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "dateiname": "client.php",
    "inhalt_base64": "...",
    "sha256": "..."
  }
}
```

---

# Unterstützte Aktionen aus der Vorlage

Das Client-Script orientiert sich an folgender Logik:

- Prozessstart melden
- Prozess-ID erhalten
- Hardwaredaten senden
- Auftrag holen
- Aktion ausführen
- Ergebnis senden
- Fehler senden
- Konfiguration synchronisieren
- Agenten lokal schreiben
- Markdown-Dateien aktualisieren
- Client-Dateien aktualisieren
- Mounts prüfen
- lokale Kommunikation einsammeln

Die konkrete Umsetzung wird auf Noob2Claw umbenannt und von `godcore_*` auf `noob2claw_*` abstrahiert.

---

# Aktionen im Client

Der Client muss mindestens diese Aktionen verstehen:

| Aktion | Aufgabe |
|---|---|
| `beenden` | Prozess sauber beenden |
| `heartbeat` | Lebenszeichen senden |
| `hardwareinfo_abholen` | Hardwaredaten lokal sammeln und senden |
| `json_holen` | lokale Agenten-/Model-Konfiguration auslesen |
| `keys_abgleichen` | API-Keys und Modellanbieter synchronisieren |
| `agenten_schreiben` | lokale Agenten in OpenClaw/Hermes/Codex schreiben |
| `mdupdate` | Markdown-Dateien in den Workspace schreiben |
| `apiupdate` | Client-Dateien aktualisieren |
| `mountcheck` | konfigurierte Mounts prüfen |
| `openclaw_befehl` | direkten CLI-Befehl ausführen |
| `chat` | Chat-Auftrag ausführen |
| `diskussion` | Diskussionsauftrag ausführen |
| `agent2agent` | Kommunikation zwischen Agenten ausführen |
| `kanban_trigger` | Kanban-Auftrag ausführen |
| `cronjob` | geplanter Auftrag |
| `aihub` | AI-Hub-Auftrag |

---

# Noob2Claw-Client Script

## Pfad

Das Script liegt auf dem Agenten-System unter:

```text
/home/noobclaw/noob2claw_client/client.php
```

Startbefehl:

```bash
/usr/bin/php-cgi -q /home/noobclaw/noob2claw_client/client.php
```

Technischer Hinweis:

Für echte Konsolenprozesse ist `php` normalerweise sauberer als `php-cgi`.  
Wenn im Video ausdrücklich `php-cgi` genutzt werden soll, muss immer `-q` verwendet werden, damit keine HTTP-Header ausgegeben werden.

---

# Client-Konfiguration

Datei:

```text
/home/noobclaw/noob2claw_client/config.php
```

Inhalt:

```php
<?php

$noob2claw_core_url = 'https://dein-core.example.com/api.php';
$noob2claw_client_uuid = 'CLIENT-UUID-HIER';
$noob2claw_client_token = 'CLIENT-TOKEN-HIER';
$noob2claw_client_version = '1.0.0';
$noob2claw_client_pfad = '/home/noobclaw/noob2claw_client/';
$noob2claw_workspace_pfad = '/home/noobclaw/noob2claw_client/workspace/';
$noob2claw_max_laufzeit_sekunden = 180;
$noob2claw_poll_min_ms = 200;
$noob2claw_poll_max_ms = 500;
$noob2claw_update_erlaubt = true;
```

Regeln:

- `config.php` wird niemals vom Core überschrieben.
- `config.example.php` darf aktualisiert werden.
- Token dürfen nicht geloggt werden.
- Fehlerausgaben maskieren sensible Werte.

---

# Laufzeitlogik des Clients

Der Client läuft als einzelner Prozess.

Ablauf:

```text
Start
  │
  ├── Lockdatei prüfen
  ├── Konfiguration laden
  ├── lokale Ordner prüfen
  ├── Prozessstart an Core melden
  ├── Hardware und Fähigkeiten melden
  │
  └── Hauptloop
        │
        ├── Heartbeat senden
        ├── Auftrag holen
        ├── wenn Auftrag vorhanden:
        │      ├── Auftrag als in Arbeit melden
        │      ├── Auftrag vollständig ausführen
        │      ├── Ergebnis oder Fehler senden
        │      └── erst danach nächsten Schritt prüfen
        │
        ├── lokale Kommunikation prüfen
        ├── Updates prüfen
        ├── Stop-Signal prüfen
        └── bei überschrittener Laufzeit sauber beenden
```

---

# 3-Minuten-Neustart ohne kaputte Aufträge

Die zentrale Regel lautet:

```text
Der Client darf nicht hart beendet werden, während er eine Aufgabe verarbeitet.
```

Der Client soll alle 3 Minuten neu starten.  
Aber ein laufender Auftrag hat Vorrang.

Richtige Logik:

```text
max_laufzeit = 180 Sekunden

wenn keine Aufgabe aktiv ist
und max_laufzeit überschritten ist:
    Prozess sauber beenden

wenn Aufgabe aktiv ist
und max_laufzeit überschritten ist:
    Aufgabe fertig ausführen
    Ergebnis senden
    danach sauber beenden
```

Falsche Logik:

```text
pkill php-cgi
kill -9
systemd RuntimeMaxSec ohne Graceful Handling
Cron startet blind mehrere Prozesse parallel
```

Diese Varianten können Aufträge zerstören oder doppelt ausführen.

---

# Locking gegen doppelte Prozesse

Es darf pro Client nur ein aktiver Prozess laufen.

Lockdatei:

```text
/home/noobclaw/noob2claw_client/run/client.lock
```

Regeln:

- Beim Start wird ein exklusiver Lock gesetzt.
- Wenn der Lock existiert und der Prozess lebt, beendet sich der neue Start sofort.
- Wenn der Lock alt ist und der Prozess nicht mehr lebt, wird der Lock bereinigt.
- Der Core erkennt trotzdem per Heartbeat, ob ein Client hängt.

---

# Empfohlener systemd-Betrieb

Besser als Cron ist ein systemd-Service, der den Client immer wieder startet.

Datei:

```text
/etc/systemd/system/noob2claw-client.service
```

Inhalt:

```ini
[Unit]
Description=Noob2Claw Client
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=noobclaw
Group=noobclaw
WorkingDirectory=/home/noobclaw/noob2claw_client
ExecStart=/usr/bin/php-cgi -q /home/noobclaw/noob2claw_client/client.php
Restart=always
RestartSec=2
KillSignal=SIGTERM
TimeoutStopSec=3600

[Install]
WantedBy=multi-user.target
```

Wichtig:

- Der Client beendet sich selbst nach ca. 180 Sekunden, wenn er frei ist.
- systemd startet ihn danach neu.
- Läuft eine Aufgabe, beendet sich der Client erst nach Abschluss der Aufgabe.
- `TimeoutStopSec=3600` verhindert, dass lange Aufgaben zu früh beendet werden.

Aktivierung:

```bash
sudo systemctl daemon-reload
sudo systemctl enable noob2claw-client.service
sudo systemctl start noob2claw-client.service
```

Logs:

```bash
journalctl -u noob2claw-client.service -f
```

---

# Alternative mit Cron

Cron ist möglich, aber schlechter kontrollierbar als systemd.

Cron-Eintrag:

```cron
*/3 * * * * /usr/bin/flock -n /home/noobclaw/noob2claw_client/run/client.lock /usr/bin/php-cgi -q /home/noobclaw/noob2claw_client/client.php >> /home/noobclaw/noob2claw_client/logs/cron.log 2>&1
```

Wichtig:

- `flock` verhindert parallele Starts.
- Der Prozess darf intern trotzdem nicht hart beendet werden.
- Lange Aufträge laufen zu Ende.
- Der nächste Cron-Start findet erst wieder statt, wenn der Lock frei ist.

---

# Signalbasiertes Beenden

Der Core kann dem Client mitteilen, dass er sich beenden soll.

API-Response:

```json
{
  "beenden": 1,
  "grund": "update"
}
```

Der Client verhält sich so:

```text
wenn keine Aufgabe aktiv:
    beende Prozess sofort sauber

wenn Aufgabe aktiv:
    merke beenden=1
    führe Aufgabe fertig aus
    sende Ergebnis
    beende Prozess danach sauber
```

---

# Update-Mechanismus

Der Client kann sich selbst aktualisieren.

Ablauf:

```text
Client
  │
  ├── fragt client_update_pruefen
  ├── erhält Manifest
  ├── lädt geänderte Dateien einzeln
  ├── prüft SHA256
  ├── schreibt Dateien zuerst nach tmp/update/
  ├── legt Backups der alten Dateien an
  ├── ersetzt Dateien atomar
  ├── meldet Update-Ergebnis
  └── beendet sich sauber, damit systemd neu startet
```

Dateien, die niemals überschrieben werden dürfen:

```text
config.php
logs/
cache/
run/
tmp/
workspace/
```

---

# Manifest

Datei auf dem Core:

```text
/var/www/noobclaw/noob2claw_client/manifest.json
```

Beispiel:

```json
{
  "version": "1.0.0",
  "erstellt_am": 1784630000,
  "dateien": [
    {
      "dateiname": "client.php",
      "sha256": "...",
      "groesse": 12345
    },
    {
      "dateiname": "funktionen.php",
      "sha256": "...",
      "groesse": 45678
    },
    {
      "dateiname": "config.example.php",
      "sha256": "...",
      "groesse": 1234
    }
  ]
}
```

---

# Lokale CLI-Erkennung

Der Client sucht beim Start nach verfügbaren Tools.

Beispiele:

```text
openclaw
hermes
codex
grok
claude
nvidia-smi
rocm-smi
amd-smi
```

Gefundene Pfade werden an den Core gemeldet.

Beispiel:

```json
{
  "openclaw_cli": "/home/noobclaw/.npm-global/bin/openclaw",
  "hermes_cli": "",
  "codex_cli": "/usr/local/bin/codex",
  "grok_cli": "",
  "claude_cli": "",
  "nvidia_smi": "/usr/bin/nvidia-smi",
  "rocm_smi": "/opt/rocm/bin/rocm-smi"
}
```

---

# Chat-Ausführung

Bei einer Chat-Aktion führt der Client den passenden API-Typ aus.

## OpenClaw CLI

```bash
openclaw agent --session-id cli --channel last --agent noob2claw_11 --json --message 'TEXT'
```

## Hermes CLI

```bash
hermes 'TEXT'
```

## Codex CLI

```bash
codex exec 'TEXT'
```

## Claude CLI

```bash
claude -p --output-format json --dangerously-skip-permissions --verbose 'TEXT'
```

Wichtig:

- Befehle werden immer mit `escapeshellarg()` abgesichert.
- Sensitive Werte werden nie ausgegeben.
- Ergebnis wird normalisiert.
- Thinking/Reasoning wird nicht unkontrolliert an die Weboberfläche gesendet.

---

# Ergebnis-Normalisierung

Unterschiedliche CLI-Tools liefern unterschiedliche Formate.

Der Client normalisiert alles auf:

```json
{
  "text": "finale Antwort",
  "dateien_bearbeitet": [],
  "tools_verwendet": [],
  "modell": "",
  "tokens_input": 0,
  "tokens_output": 0,
  "kosten": 0,
  "rohantwort": {}
}
```

---

# Logging

Der Client loggt lokal und im Core.

Lokale Datei:

```text
/home/noobclaw/noob2claw_client/logs/client.log
```

Core-Tabellen:

```text
agenten_client_log
agenten_auftraege
agenten_client_prozesse
```

Log-Level:

```text
debug
info
warning
error
critical
```

Regel:

Sensible Werte werden maskiert.

---

# Sicherheit

Pflichtregeln:

- Kein öffentlicher Download ohne Authentifizierung.
- Keine Tokens in Logs.
- Keine Secrets in JavaScript.
- Keine direkten SQL-Zugriffe in `api.php`.
- Keine Shell-Befehle ohne `escapeshellarg()`.
- Keine parallele Verarbeitung desselben Auftrags.
- Keine harten Kills bei aktiver Aufgabe.
- Updates nur nach SHA256-Prüfung.
- `config.php` wird nie überschrieben.
- Client läuft nicht als root.

---

# Rechte

Neue Rechte:

```text
agents_anzeigen
agents_anlegen
agents_bearbeiten
agents_deaktivieren
agents_loeschen
agents_testen
agenten_clients_anzeigen
agenten_clients_verwalten
agenten_clients_token_erzeugen
agenten_clients_update
agenten_auftraege_anzeigen
agenten_auftraege_verwalten
agenten_log_anzeigen
```

Administrator erhält alle Rechte.

---

# Navigationsseiten

Neue Seiten:

```text
nav/work_agents.php
nav/work_agenten_chat.php
nav/settings_agents.php
nav/settings_agenten_clients.php
nav/control_agenten_auftraege.php
nav/control_agenten_client_log.php
nav/control_agenten_client_prozesse.php
nav/control_agenten_updates.php
```

---

# Funktionsdateien

Neue oder erweiterte Funktionsdateien:

```text
inc/funktionen_agents.php
inc/funktionen_agenten_clients.php
inc/funktionen_agenten_auftraege.php
inc/funktionen_agenten_client_log.php
inc/funktionen_agenten_client_prozesse.php
inc/funktionen_agenten_client_updates.php
inc/funktionen_client_api.php
inc/funktionen_client_update.php
inc/funktionen_cli_ergebnis.php
```

Regeln:

- Jede Tabelle hat genau eine fachliche Funktionsdatei.
- `api.php` routet nur.
- Business-Logik liegt in `inc/`.
- Keine SQL-Abfragen in `nav/`.

---

# Minimaler Entwicklungsauftrag für den KI-Agenten

Der KI-Agent soll in Folge 10 mindestens erstellen:

1. Datenbanktabellen für Clients, Aufträge, Prozesse, Logs und Updates
2. Funktionsdateien für Agenten-Clients und Aufträge
3. Weboberfläche für Agentenverwaltung
4. Weboberfläche für Clientverwaltung
5. Weboberfläche für Auftrags- und Prozesslogs
6. API-Aktionen für den Client
7. Client-Verzeichnis auf dem Core
8. erstes `client.php`
9. `config.example.php`
10. `manifest.json`
11. Installationshinweis für systemd
12. sichere 3-Minuten-Neustartlogik
13. Update-Prüfung des Clients
14. Hardwareinfo-Erkennung
15. einfacher Testauftrag `heartbeat` oder `chat`

---

# Testablauf für Folge 10

## Schritt 1: Client im Core anlegen

Im Webinterface wird ein neuer Client erzeugt.

Ergebnis:

- Client-ID
- Client-UUID
- Client-Token
- Installationsbefehl

---

## Schritt 2: Client auf Agenten-System installieren

Pfad:

```bash
mkdir -p /home/noobclaw/noob2claw_client
```

Client-Dateien laden.

`config.php` erstellen.

---

## Schritt 3: Client starten

```bash
/usr/bin/php-cgi -q /home/noobclaw/noob2claw_client/client.php
```

Erwartung:

- Prozessstart im Core sichtbar
- Heartbeat sichtbar
- Hardwareinfo sichtbar
- Version sichtbar

---

## Schritt 4: Testauftrag erstellen

Im Core einen Testauftrag erzeugen:

```text
Aktion: chat
Agent: Test-Agent
Text: Antworte mit einem kurzen Test.
```

Erwartung:

- Auftrag wird reserviert
- Client führt ihn aus
- Ergebnis erscheint im Core
- Logs sind sichtbar

---

## Schritt 5: 3-Minuten-Neustart prüfen

Erwartung ohne Aufgabe:

```text
Client beendet sich nach ca. 180 Sekunden sauber.
systemd startet ihn neu.
```

Erwartung mit langer Aufgabe:

```text
Client läuft länger als 180 Sekunden.
Aufgabe wird fertiggestellt.
Ergebnis wird gesendet.
Erst danach beendet sich der Client.
```

---

# Wichtigste Aussage für das Video

Das ist der Schritt, in dem aus dem Webinterface eine echte Agenten-Zentrale wird.

Vorher:

```text
Noob2Claw verwaltet Daten.
```

Nachher:

```text
Noob2Claw steuert echte KI-Agenten auf echten Servern.
```

---

# Zielzustand nach Folge 10

Nach Folge 10 kann das System:

- Agenten im Webinterface verwalten
- Clients registrieren
- Client-Prozesse überwachen
- Aufträge an Clients verteilen
- Ergebnisse zurücknehmen
- Hardwareinformationen anzeigen
- Client-Scripte zentral ausliefern
- Client-Updates vorbereiten
- Agenten-CLIs lokal ansprechen
- Aufgaben sicher abschließen, bevor ein Neustart erfolgt

