# 1_Agentenchat.md

# Noob2Claw – Folge 11: Agentenchat

Version: 1.0  
Bereich: Folge 11  
Zielsystem: Noob2Claw Core + vorhandene Agenten-Clients  
Technologien: PHP, MariaDB, Apache, JavaScript, CSS, OpenClaw, Hermes, Codex, Grok, Claude CLI

---

# Ziel

In Folge 10 wurde aus Noob2Claw eine steuerbare Agenten-Zentrale:

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

In Folge 11 wird diese Infrastruktur für Benutzer nutzbar.

Das Ziel ist ein professioneller Agentenchat im Webinterface.

Der Benutzer wählt links einen Agenten aus und schreibt rechts mit diesem Agenten.

Die Oberfläche soll optisch klar, modern und hochwertig wirken:

```text
links: Agenten
rechts: Chat
```

Nachrichten werden wie in modernen Messengern dargestellt:

- Benutzer rechts
- Agent links
- Systemmeldungen mittig
- Sprechblasen
- Avatar/Bild beim Agenten
- Status, Ladezustände und Fehler sichtbar

---

# Grundprinzip

Der Chat ist nur die Oberfläche.

Die eigentliche KI-Ausführung bleibt bei der in Folge 10 aufgebauten Agenten- und Clientstruktur.

Der Browser spricht niemals direkt mit einem Agenten-Client und niemals direkt mit OpenClaw, Hermes, Codex, Grok oder Claude CLI.

Richtiger Ablauf:

```text
Benutzer
  │
  ▼
Noob2Claw Chat UI
  │
  ▼
Noob2Claw Core
  │
  ▼
agenten_auftraege
  │
  ▼
Noob2Claw Client
  │
  ▼
lokale KI-CLI
  │
  ▼
Noob2Claw Core
  │
  ▼
Chat UI
```

---

# Begriffe

| Begriff | Bedeutung |
|---|---|
| Agent | logische KI-Rolle aus der Agentenverwaltung |
| Chat | Unterhaltung zwischen Benutzer und Agent |
| Nachricht | einzelne Chatnachricht |
| Bubble | sichtbare Sprechblase im Chat |
| Auftrag | technische Aufgabe für einen Agenten-Client |
| Avatar | Bild oder Initialen eines Agenten |
| Anhang | Datei, Bild oder Dokument im Chat |
| Status | Zustand von Chat, Nachricht, Agent oder Auftrag |
| Polling | regelmäßiges Nachladen neuer Nachrichten per JavaScript |

---

# Zielbild der Oberfläche

```text
┌────────────────────────────────────────────────────────────────────────────┐
│ Header / Noob2Claw                                                         │
├──────────────────────┬─────────────────────────────────────────────────────┤
│ Agenten              │ Chat mit ausgewähltem Agenten                       │
│                      │                                                     │
│ Suche                │ Agentenheader                                       │
│ Filter               │ Bild | Name | Rolle | Modell | Clientstatus         │
│                      │                                                     │
│ ┌ Agent Avatar ┐     │ ┌───────────────────────────────────────────────┐   │
│ │ Name         │     │ │ Agentenantwort links                           │   │
│ │ Rolle        │     │ └───────────────────────────────────────────────┘   │
│ │ Status       │     │                           ┌──────────────────────┐  │
│ └──────────────┘     │                           │ Benutzer rechts       │  │
│                      │                           └──────────────────────┘  │
│ ┌ Agent Avatar ┐     │ ┌───────────────────────────────────────────────┐   │
│ │ Name         │     │ │ Agentenantwort links                           │   │
│ └──────────────┘     │ └───────────────────────────────────────────────┘   │
│                      │                                                     │
│                      │ Uploads | Texteingabe | Senden                     │
└──────────────────────┴─────────────────────────────────────────────────────┘
```

---

# Architektur

```text
Browser
  │
  ├── nav/work_agenten_chat.php
  ├── js/agenten_chat.js
  └── css/agenten_chat.css

Noob2Claw Core
  │
  ├── api.php
  ├── inc/funktionen_agenten_chat.php
  ├── inc/funktionen_agenten_chat_nachrichten.php
  ├── inc/funktionen_agenten_chat_dateien.php
  ├── inc/funktionen_agenten_chat_status.php
  ├── inc/funktionen_uploads.php
  └── vorhandene Agenten-/Auftragsfunktionen aus Folge 10

Datenbank
  │
  ├── agents
  ├── agenten_clients
  ├── agenten_auftraege
  ├── agenten_chats
  ├── agenten_chat_nachrichten
  ├── agenten_chat_dateien
  └── agenten_chat_status

Agenten-Client
  │
  └── verarbeitet Auftrag aktion=chat
```

---

# Abgrenzung

Diese Folge baut den Agentenchat.

Nicht Bestandteil dieser Folge:

- Gruppenchats mit mehreren Benutzern
- öffentliche Chatfreigaben
- vollständige Knowledge-Base
- Vektorindex
- RAG-System
- Live-WebSocket-Push als Pflicht
- Audiochat
- Videotelefonie
- mobile App
- externe Chatplattformen
- Direktzugriff auf lokale CLI aus dem Browser

Erlaubt ist:

- solide AJAX-Polling-Architektur
- vorbereitete Erweiterbarkeit für WebSocket
- Dateiuploads
- Agentenbilder
- Statusanzeigen
- persistente Chathistorie
- Chat-Aufträge über die bestehende Agenten-Auftragsverwaltung

---

# Verzeichnisstruktur

## Neue oder erweiterte Dateien im Core

```text
/var/www/noobclaw/
├── nav/
│   └── work_agenten_chat.php
├── inc/
│   ├── funktionen_agenten_chat.php
│   ├── funktionen_agenten_chat_nachrichten.php
│   ├── funktionen_agenten_chat_dateien.php
│   ├── funktionen_agenten_chat_status.php
│   └── funktionen_uploads.php
├── js/
│   └── agenten_chat.js
├── css/
│   └── agenten_chat.css
├── uploads/
│   ├── agents/
│   │   └── avatars/
│   └── agenten_chat/
└── sql/
    └── migrations/
        └── 2026_07_21_folge_11_agentenchat.sql
```

Wenn das Projekt bereits ein anderes Migrationsschema nutzt, ist dieses bestehende Schema zu verwenden.

---

# Navigationsseite

Neue Seite:

```text
nav/work_agenten_chat.php
```

Metadaten:

```php
<?php
// TITLE: Agentenchat
// ICON: chat
// BEREICH: Work
// PRIO: 20
// RECHT: agenten_chat_anzeigen
?>
```

Die Seite enthält nur Struktur und Platzhalter für Datencontainer.

Keine SQL-Abfragen.

Keine Business-Logik.

Keine Inline-CSS-Blöcke.

Kein Inline-JavaScript.

---

# UI-Aufbau

## Linke Spalte: Agentenliste

Pflichtbestandteile:

- Überschrift `Agenten`
- Suchfeld
- Filter `Alle`, `Aktiv`, `Online`, `Favoriten`
- Agentenbild oder Initialen
- Agentenname
- Rolle oder Beschreibung
- Statusbadge
- letzter Nachrichten-Auszug
- Zeit der letzten Aktivität
- Hinweis bei fehlendem Client
- aktive Auswahl visuell eindeutig
- Empty State, wenn keine Agenten vorhanden sind

## Rechte Spalte: Chat

Pflichtbestandteile:

- Chatheader
- Agentenbild
- Agentenname
- Rolle
- Modell
- API-Typ
- Clientstatus
- Status, ob Agent gerade arbeitet
- Nachrichtenbereich
- Systemmeldungen
- Eingabebereich
- Uploadbutton
- Vorschau ausgewählter Dateien
- Sendenbutton
- Hinweis auf fehlende Berechtigung
- Empty State, wenn kein Agent ausgewählt ist

---

# Sprechblasen

## Benutzer-Nachrichten

- rechts ausgerichtet
- klar als Benutzer erkennbar
- Zeitstempel sichtbar
- Status sichtbar
- Anhänge sichtbar
- Fehler sichtbar

## Agenten-Nachrichten

- links ausgerichtet
- Agentenavatar daneben
- Agentenname optional oberhalb
- Zeitstempel sichtbar
- Modell/API-Typ optional sichtbar
- Anhänge sichtbar
- Fehler sichtbar

## Systemmeldungen

- mittig
- dezent
- nicht als normale Chatantwort tarnen

Beispiele:

```text
Agent arbeitet...
Auftrag wurde erstellt.
Client ist offline.
Antwort konnte nicht erzeugt werden.
Datei wurde hochgeladen.
```

---

# Statuslogik

## Chatstatus

| Status | Bedeutung |
|---|---|
| `offen` | normaler Chat |
| `wartet` | Nachricht gesendet, Auftrag offen |
| `arbeitet` | Client verarbeitet Auftrag |
| `fehler` | letzter Auftrag fehlgeschlagen |
| `archiviert` | Chat archiviert |

## Nachrichtenstatus

| Status | Bedeutung |
|---|---|
| `erstellt` | Nachricht lokal gespeichert |
| `gesendet` | Auftrag erzeugt |
| `wartet` | wartet auf Agentenantwort |
| `in_arbeit` | Client verarbeitet |
| `erledigt` | Antwort vorhanden |
| `fehler` | Verarbeitung fehlgeschlagen |

---

# Datenbank

## Tabelle `agenten_chats`

Aufgabe:

Speichert eine Unterhaltung zwischen einem Benutzer und einem Agenten.

Pflichtfelder:

```text
id
uuid
benutzer_id
agent_id
titel
status
favorit
archiviert
geloescht
letzte_nachricht_id
letzte_nachricht_text
letzte_nachricht_am
offener_auftrag_id
erstellt_am
geaendert_am
```

Empfohlene Indizes:

```text
uuid
benutzer_id
agent_id
status
archiviert
geloescht
letzte_nachricht_am
```

Regeln:

- Ein Benutzer kann pro Agent mehrere Chats haben, wenn später benötigt.
- Für Folge 11 reicht ein Standardchat pro Benutzer und Agent.
- Kein Chat wird hart gelöscht.
- Gelöschte Chats werden per `geloescht=1` ausgeblendet.

---

## Tabelle `agenten_chat_nachrichten`

Aufgabe:

Speichert alle Nachrichten eines Chats.

Pflichtfelder:

```text
id
uuid
chat_id
agent_id
benutzer_id
client_id
auftrag_id
absender_typ
richtung
nachricht_typ
status
text
text_roh
payload_json
dateien_json
metadaten_json
fehlertext
tokens_input
tokens_output
modell
api_typ
erstellt_am
geaendert_am
gesendet_am
gelesen_am
```

Erlaubte Werte für `absender_typ`:

```text
benutzer
agent
system
client
```

Erlaubte Werte für `richtung`:

```text
eingang
ausgang
system
```

Erlaubte Werte für `nachricht_typ`:

```text
text
datei
bild
system
fehler
```

Regeln:

- Benutzer schreibt rechts: `absender_typ=benutzer`, `richtung=ausgang`.
- Agent antwortet links: `absender_typ=agent`, `richtung=eingang`.
- Systemmeldungen: `absender_typ=system`, `richtung=system`.
- Antwort vom Client wird über `auftrag_id` zugeordnet.
- HTML wird nicht ungeprüft gespeichert oder ausgegeben.
- Originaltext darf in `text_roh` gespeichert werden, wenn er für Debugging oder spätere Normalisierung benötigt wird.

---

## Tabelle `agenten_chat_dateien`

Aufgabe:

Speichert Datei- und Bildanhänge des Agentenchats.

Pflichtfelder:

```text
id
uuid
chat_id
nachricht_id
agent_id
benutzer_id
auftrag_id
dateiname_original
dateiname_speicher
mime_type
endung
groesse_bytes
sha256
speicher_pfad
thumbnail_pfad
datei_typ
status
sichtbar
metadaten_json
scan_status
scan_meldung
erstellt_am
geaendert_am
```

Erlaubte Werte für `datei_typ`:

```text
bild
dokument
text
archiv
sonstiges
```

Regeln:

- Originalname wird nur angezeigt, nicht als Speichername genutzt.
- Speichername wird sicher zufällig erzeugt.
- Datei wird logisch einer Nachricht zugeordnet.
- Dateien ohne Nachricht sind nur temporär zulässig.
- Keine PHP-Dateien zulassen.
- Keine ausführbaren Dateien zulassen.
- Keine direkten absoluten Pfade an den Browser senden.

---

## Tabelle `agenten_chat_status`

Aufgabe:

Speichert laufende Statusinformationen pro Chat und Benutzer.

Pflichtfelder:

```text
id
chat_id
benutzer_id
agent_id
status
arbeitet
letzter_poll
letzter_auftrag_id
letzte_gelesene_nachricht_id
tippt
info_json
erstellt_am
geaendert_am
```

Regeln:

- Dient der schnellen Anzeige im Frontend.
- Ist kein Ersatz für die eigentlichen Nachrichten.
- Kann aus den Nachrichten und Aufträgen neu aufgebaut werden.

---

# Erweiterung Tabelle `agents`

Prüfe, ob die Tabelle `agents` bereits diese Felder besitzt oder ob sie ergänzt werden müssen:

```text
avatar_datei_id
avatar_pfad
avatar_initialen
avatar_farbe
chat_aktiv
chat_sortierung
```

Regeln:

- Bestehende Felder nicht überschreiben.
- Wenn Folge 10 andere Feldnamen nutzt, bestehende Struktur respektieren.
- Agentenbild ist optional.
- Fehlt ein Bild, werden Initialen angezeigt.
- `chat_aktiv=0` blendet den Agenten im Chat aus, ohne ihn global zu deaktivieren.

---

# Rechte

Neue Rechte:

```text
agenten_chat_anzeigen
agenten_chat_schreiben
agenten_chat_dateien_hochladen
agenten_chat_dateien_anzeigen
agenten_chat_verwalten
agenten_chat_loeschen
agenten_chat_agentenbild_hochladen
agenten_chat_status_anzeigen
```

Administrator erhält alle Rechte.

Pflicht:

- Rechte in Navigation prüfen.
- Rechte in API prüfen.
- Rechte in Business-Funktionen bei kritischen Aktionen prüfen.
- Keine reine Frontend-Sperre.
- Bei fehlenden Rechten neutrale Fehlermeldung ausgeben.

---

# API-Aktionen

Alle Aktionen laufen über:

```text
/api.php
```

## `agenten_liste`

Request:

```json
{
  "modul": "agenten_chat",
  "aktion": "agenten_liste",
  "suche": "",
  "filter": "alle"
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "agenten": []
  },
  "request_id": ""
}
```

Daten pro Agent:

```json
{
  "agent_id": 1,
  "titel": "Claudia Developer",
  "rolle": "Entwicklung",
  "beschreibung": "",
  "avatar_url": "",
  "avatar_initialen": "CD",
  "status": "online",
  "modell": "",
  "api_typ": "openclaw_cli",
  "client_id": 1,
  "client_status": "online",
  "letzte_nachricht": "",
  "letzte_aktivitaet": 0,
  "offene_auftraege": 0
}
```

---

## `chat_oeffnen`

Request:

```json
{
  "modul": "agenten_chat",
  "aktion": "chat_oeffnen",
  "agent_id": 1
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "chat": {},
    "nachrichten": [],
    "agent": {}
  },
  "request_id": ""
}
```

Regeln:

- Wenn kein Chat existiert, wird ein Chat für Benutzer und Agent erzeugt.
- Der Chat wird nicht erzeugt, wenn der Agent nicht existiert oder für Chat deaktiviert ist.
- Rechte werden geprüft.

---

## `nachrichten_liste`

Request:

```json
{
  "modul": "agenten_chat",
  "aktion": "nachrichten_liste",
  "chat_id": 1,
  "seit_nachricht_id": 0
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "nachrichten": [],
    "chat_status": {}
  },
  "request_id": ""
}
```

---

## `nachricht_senden`

Request:

```json
{
  "modul": "agenten_chat",
  "aktion": "nachricht_senden",
  "chat_id": 1,
  "agent_id": 1,
  "text": "Schreibe eine kurze Antwort.",
  "dateien": []
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "nachricht_id": 10,
    "auftrag_id": 55,
    "status": "wartet"
  },
  "request_id": ""
}
```

Pflichtlogik:

1. Recht prüfen.
2. Agent prüfen.
3. Chat prüfen.
4. Nachricht speichern.
5. Chat-Kontext erstellen.
6. Auftrag in `agenten_auftraege` erstellen.
7. Nachricht mit `auftrag_id` aktualisieren.
8. Chatstatus auf `wartet` setzen.
9. Antwort zurückgeben.

---

## `datei_hochladen`

Request:

```text
multipart/form-data
```

Felder:

```text
modul=agenten_chat
aktion=datei_hochladen
chat_id=1
agent_id=1
datei=<file>
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "datei_id": 7,
    "dateiname_original": "bild.png",
    "datei_typ": "bild",
    "groesse_bytes": 12345
  },
  "request_id": ""
}
```

---

## `agentenbild_hochladen`

Request:

```text
multipart/form-data
```

Felder:

```text
modul=agenten_chat
aktion=agentenbild_hochladen
agent_id=1
datei=<file>
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "agent_id": 1,
    "avatar_url": "/uploads/agents/avatars/..."
  },
  "request_id": ""
}
```

---

## `status`

Request:

```json
{
  "modul": "agenten_chat",
  "aktion": "status",
  "chat_id": 1
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {
    "chat_status": "wartet",
    "arbeitet": 1,
    "offener_auftrag_id": 55,
    "client_status": "arbeitet"
  },
  "request_id": ""
}
```

---

## `chat_archivieren`

Request:

```json
{
  "modul": "agenten_chat",
  "aktion": "chat_archivieren",
  "chat_id": 1
}
```

Response:

```json
{
  "erfolg": true,
  "meldung": "Chat wurde archiviert.",
  "daten": {},
  "request_id": ""
}
```

---

# Verbindung zu `agenten_auftraege`

Beim Senden einer Nachricht wird ein Auftrag erzeugt.

Mindestwerte:

```text
aktion = chat
referenz = agenten_chat
referenz_id = nachricht_id
agent_id = agent_id
client_id = bevorzugter Client des Agenten oder 0
status = offen
text = aktuelle Benutzernachricht
payload_json = Chat-Kontext
dateien_json = zugeordnete Dateien
```

Payload-Beispiel:

```json
{
  "chat_id": 1,
  "nachricht_id": 10,
  "agent_id": 1,
  "benutzer_id": 1,
  "systemprompt": "",
  "chat_verlauf": [],
  "aktuelle_nachricht": "Schreibe eine kurze Antwort.",
  "dateien": [],
  "antwortformat": {
    "typ": "text",
    "markdown_erlaubt": true
  }
}
```

---

# Ergebnisverarbeitung

Wenn der Client ein Ergebnis für einen Chat-Auftrag zurücksendet, muss der Core zusätzlich zur Auftragsverwaltung den Chat aktualisieren.

Pflichtlogik:

1. Auftrag finden.
2. Prüfen, ob `referenz=agenten_chat`.
3. Ursprüngliche Benutzernachricht finden.
4. Ergebnis normalisieren.
5. Agentenantwort in `agenten_chat_nachrichten` speichern.
6. Benutzernachricht auf `erledigt` setzen.
7. Chatstatus aktualisieren.
8. `letzte_nachricht_*` aktualisieren.
9. offene Auftrag-ID entfernen.
10. Fehler und Logs sauber speichern.

Bei Fehler:

1. Benutzernachricht auf `fehler` setzen.
2. System- oder Fehlernachricht speichern.
3. Chatstatus auf `fehler` setzen.
4. Fehlertext maskieren und anzeigen.
5. technischen Fehler vollständig, aber ohne Secrets, loggen.

---

# JavaScript-Datei

Datei:

```text
js/agenten_chat.js
```

Pflichtfunktionen:

```text
agentenChatInitialisieren()
agentenChatAgentenLaden()
agentenChatAgentAuswaehlen(agentId)
agentenChatNachrichtenLaden()
agentenChatNachrichtSenden()
agentenChatDateiHochladen()
agentenChatAgentenbildHochladen()
agentenChatStatusAktualisieren()
agentenChatPollingStarten()
agentenChatPollingStoppen()
agentenChatFehlerAnzeigen()
agentenChatScrollenWennUnten()
```

Regeln:

- `fetch()` verwenden.
- JSON-Antworten sauber prüfen.
- kein Token im JavaScript.
- keine Secrets im DOM.
- CSRF-Mechanik des bestehenden Projekts verwenden.
- Polling pausiert bei verstecktem Browser-Tab.
- Polling verlangsamt sich nach Fehlern.
- Doppelsenden verhindern.
- Enter sendet.
- Shift+Enter erzeugt Zeilenumbruch.
- Datei-Uploads per `FormData`.
- Nachrichten clientseitig anhand ID deduplizieren.
- Texte sicher einfügen, nicht per ungeprüftem `innerHTML`.

---

# CSS-Datei

Datei:

```text
css/agenten_chat.css
```

Pflichtklassen:

```text
.agenten-chat
.agenten-chat__sidebar
.agenten-chat__suche
.agenten-chat__filter
.agenten-chat__agent
.agenten-chat__agent--aktiv
.agenten-chat__avatar
.agenten-chat__status
.agenten-chat__main
.agenten-chat__header
.agenten-chat__messages
.agenten-chat__message
.agenten-chat__message--user
.agenten-chat__message--agent
.agenten-chat__message--system
.agenten-chat__bubble
.agenten-chat__attachments
.agenten-chat__composer
.agenten-chat__textarea
.agenten-chat__send
.agenten-chat__upload
.agenten-chat__empty
.agenten-chat__loading
.agenten-chat__error
```

Regeln:

- Design Tokens verwenden.
- Light Mode und Dark Mode unterstützen.
- keine Inline-Styles.
- responsive.
- links/rechts sauber ausrichten.
- lange Texte umbrechen.
- Codeblöcke lesbar.
- Mobile Ansicht darf nicht kaputt laufen.

---

# Uploads

## Agentenbilder

Pfad:

```text
uploads/agents/avatars/
```

Erlaubte Typen:

```text
image/png
image/jpeg
image/webp
```

Maximale Größe:

```text
5 MB
```

Regeln:

- echte MIME-Prüfung
- zufälliger Dateiname
- SHA256 speichern
- alte Avatare nicht unkontrolliert löschen
- Cache-Busting im Frontend
- Fallback Initialen

## Chat-Dateien

Pfad:

```text
uploads/agenten_chat/
```

Erlaubte Typen in Folge 11:

```text
image/png
image/jpeg
image/webp
text/plain
application/pdf
application/json
text/markdown
```

Maximale Größe:

```text
25 MB
```

Regeln:

- ausführbare Dateien blockieren
- PHP-Dateien blockieren
- HTML-Dateien blockieren
- Script-Dateien blockieren
- Originalnamen nur anzeigen
- Speichername randomisieren
- Uploads mit Benutzer, Agent, Chat und Nachricht verbinden

---

# Sicherheit

Pflichtregeln:

- CSRF-Schutz für alle schreibenden Aktionen
- Rechteprüfung serverseitig
- XSS-Schutz bei Chatnachrichten
- Markdown sicher rendern oder zunächst nur escaped anzeigen
- keine Secrets im JavaScript
- keine Tokens in URLs
- keine absoluten Serverpfade im Browser
- Uploadverzeichnisse gegen PHP-Ausführung schützen
- keine SQL-Abfragen mit ungesicherten Eingaben
- keine Direktkommunikation Browser zu Client
- keine Shellausführung aus Chatnachrichten im Core
- Aufträge gegen Doppelanlage bei Doppelklick absichern
- technische Fehler loggen, aber nicht roh anzeigen
- sensible Felder maskieren

---

# Markdown und Code

Agenten können Markdown und Code zurückgeben.

Folge 11 muss mindestens:

- Zeilenumbrüche erhalten
- Codeblöcke lesbar darstellen
- HTML escapen
- lange Codezeilen scrollbar machen
- keine ungeprüfte HTML-Ausführung erlauben
- Links entweder escapen oder sicher rendern

Eine vollständige Markdown-Bibliothek ist nicht Pflicht.

Wenn eine Bibliothek verwendet wird, muss sie sicher eingebunden und dokumentiert werden.

---

# Leere Zustände

Pflicht-Empty-States:

- keine Agenten vorhanden
- kein Agent ausgewählt
- Chat noch leer
- Agent hat keinen Client
- Agent ist deaktiviert
- Client ist offline
- Antwort konnte nicht erzeugt werden
- Upload wurde abgelehnt

Keine falschen Beispieldaten anzeigen.

---

# Tests

## Syntax

```bash
find /mnt/noob2claw \
-type f \
-name '*.php' \
-print0 | xargs -0 -n1 php -l
```

## Navigation

Prüfen:

```text
/index.php?seite=work_agenten_chat
```

## API

Prüfen:

```text
/api.php?modul=agenten_chat&aktion=agenten_liste
/api.php?modul=agenten_chat&aktion=status
```

## Funktion

Mindesttests:

1. Seite öffnet.
2. Agentenliste lädt.
3. Chat kann geöffnet werden.
4. Nachricht kann gesendet werden.
5. Benutzer-Nachricht erscheint rechts.
6. Auftrag wird erstellt.
7. Status zeigt wartende Antwort.
8. Agenten-Client verarbeitet Auftrag.
9. Antwort erscheint links.
10. Fehler erscheint als Fehlernachricht.
11. Agentenbild kann hochgeladen werden.
12. Avatar erscheint in Liste und Chat.
13. Datei kann hochgeladen werden.
14. unzulässige Datei wird abgelehnt.
15. XSS-Test wird escaped.
16. Benutzer ohne Recht sieht die Seite nicht.
17. API ohne Recht lehnt ab.
18. Mobile Ansicht ist nutzbar.

---

# Dokumentation

Erstelle oder aktualisiere:

```text
/mnt/noob2claw/README.md
/mnt/noob2claw/docs/folge_11_agentenchat/README.md
```

Inhalt:

- Ziel
- Architektur
- Datenbanktabellen
- Navigationsseite
- API-Aktionen
- Uploadpfade
- Chatablauf
- Auftragserzeugung
- Ergebnisverarbeitung
- Rechte
- Sicherheit
- Tests
- bekannte Grenzen

---

# Minimaler Entwicklungsauftrag

Der KI-Agent soll in Folge 11 mindestens erstellen:

1. Datenbanktabellen für Chat, Nachrichten, Dateien und Status
2. Erweiterung der Agententabelle um Avatar-/Chatfelder, falls nötig
3. Navigationsseite `work_agenten_chat`
4. Funktionsdateien für Chatlogik
5. API-Aktionen für Chat
6. JavaScript-Datei für Chatinteraktion
7. CSS-Datei für Chatdesign
8. Uploadpfade für Agentenbilder
9. Uploadpfade für Chatdateien
10. sichere Uploadlogik
11. Verbindung zur bestehenden Agenten-Auftragsverwaltung
12. Speicherung von Agentenantworten im Chat
13. Fehleranzeige im Chat
14. Rechteintegration
15. Tests und Abschlussbericht

---

# Wichtigste Aussage für das Video

Vorher:

```text
Noob2Claw konnte Agenten verwalten und Aufträge verteilen.
```

Nachher:

```text
Noob2Claw bekommt ein echtes Chat-Interface, mit dem wir direkt mit unseren eigenen Agenten sprechen können.
```

---

# Zielzustand nach Folge 11

Nach Folge 11 kann das System:

- Agenten links in einer professionellen Liste anzeigen
- mit einem Agenten rechts chatten
- Nachrichten dauerhaft speichern
- Benutzer-Nachrichten rechts darstellen
- Agenten-Antworten links darstellen
- Systemmeldungen sauber anzeigen
- Agentenbilder hochladen
- Agenten persönlicher darstellen
- Chat-Dateien hochladen
- Chatnachrichten als Aufträge an Agenten-Clients geben
- Agentenantworten automatisch dem Chat zuordnen
- Fehler sichtbar machen
- Berechtigungen sauber prüfen
- den Chat später auf WebSocket, RAG oder mehrere Agenten erweitern
