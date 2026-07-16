# 11_Datenbanktabellen_Core.md

# Datenbanktabellen – Core

Version: 1.0  
Datenbanksystem: MariaDB

Dieses Dokument definiert die zentralen Core-Tabellen der Anwendung.

Die Core-Datenbank umfasst:

- Benutzerverwaltung
- Benutzergruppen und Rechte
- Benutzeraktivitäten und Logins
- Sessions
- API-Benutzer und API-Logging
- MCP-Benutzer und MCP-Logging
- Agenten und Sub-Agenten
- Datenbankfehler
- Systemereignisse
- Einstellungen

Fachmodule wie Modelle, Prompts, Workflows oder Scheduler werden später in separaten Dateien definiert.

---

# Verbindliche Grundregeln

Für alle Tabellen gelten folgende Vorgaben:

- Tabellen- und Spaltennamen werden auf Deutsch angelegt.
- Tabellen- und Spaltennamen werden kleingeschrieben.
- Wörter werden mit Unterstrichen getrennt.
- Jede Tabelle besitzt einen Primärschlüssel `id`.
- Primärschlüssel verwenden `BIGINT UNSIGNED`.
- Fremdschlüssel verwenden ebenfalls `BIGINT UNSIGNED`.
- Alle Tabellen verwenden `InnoDB`.
- Alle Texte verwenden `utf8mb4`.
- Passwörter und Zugangsschlüssel werden niemals im Klartext gespeichert.
- `NULL` wird grundsätzlich nicht verwendet.
- Nicht gesetzte numerische Werte werden mit `0` gespeichert.
- Nicht gesetzte Texte werden mit einem leeren String gespeichert.
- Nicht gesetzte JSON-Werte werden als `{}` oder `[]` gespeichert.
- Zeitwerte werden als Unix-Timestamp in `BIGINT UNSIGNED` gespeichert.
- Der Wert `0` bedeutet bei Zeitfeldern: nicht gesetzt.
- Optionale Fremdschlüssel verwenden `0`.
- Beziehungen mit optionalen IDs werden in der Business-Logik geprüft.

---

# Tabellenübersicht

```text
benutzer
benutzer_gruppen
benutzer_gruppen_zuordnung
rechte
benutzer_gruppen_rechte

benutzer_log
benutzer_logins
sessions

api_benutzer
api_log

mcp_benutzer
mcp_log

agents
agents_log

db_fehler_log
system_log

einstellungen
```

---

# 1. Tabelle `benutzer`

## Beschreibung

Die Tabelle `benutzer` enthält ausschließlich Benutzer der Weboberfläche.

API- und MCP-Benutzer werden bewusst in eigenen Tabellen gespeichert, da diese andere Zugangsdaten, Sicherheitsregeln und Protokollierungsanforderungen besitzen.

Die Tabelle dient als Grundlage für:

- Anmeldung an der Weboberfläche
- Benutzerverwaltung
- Benutzergruppen
- Rechteprüfung
- Sessions
- benutzerspezifische Einstellungen
- Aktivitätsprotokolle
- Loginprotokolle

## Vorgesehene Spalten

```text
id
benutzername
passwort_hash
vorname
nachname
anzeigename
email
aktiv
gesperrt
letzte_anmeldung
erstellt_am
geaendert_am
```

## Hinweise

- `benutzername` muss eindeutig sein.
- `email` sollte ebenfalls eindeutig sein.
- `passwort_hash` enthält ausschließlich einen mit `password_hash()` erzeugten Wert.
- `aktiv = 0` verhindert die Anmeldung.
- `gesperrt = 1` sperrt den Benutzer unabhängig vom Aktivstatus.
- Benutzer werden grundsätzlich deaktiviert und nicht physisch gelöscht.

---

# 2. Tabelle `benutzer_gruppen`

## Beschreibung

Die Tabelle `benutzer_gruppen` enthält die verfügbaren Benutzergruppen.

Beispiele:

```text
Administrator
Entwickler
Support
Benutzer
Gast
```

Eine Gruppe bündelt mehrere Rechte.

## Vorgesehene Spalten

```text
id
bezeichnung
beschreibung
prioritaet
aktiv
erstellt_am
geaendert_am
```

## Hinweise

- `bezeichnung` muss eindeutig sein.
- `prioritaet` dient zur Sortierung.
- `aktiv = 0` deaktiviert die Gruppe, ohne vorhandene Zuordnungen zu löschen.

---

# 3. Tabelle `benutzer_gruppen_zuordnung`

## Beschreibung

Diese Tabelle ordnet Benutzer einer oder mehreren Benutzergruppen zu.

Ein Benutzer kann mehreren Gruppen angehören.

Eine Gruppe kann mehreren Benutzern zugeordnet sein.

## Vorgesehene Spalten

```text
id
benutzer_id
benutzer_gruppe_id
erstellt_von
erstellt_am
```

## Eindeutigkeit

Die Kombination aus:

```text
benutzer_id
benutzer_gruppe_id
```

muss eindeutig sein.

Dadurch wird verhindert, dass dieselbe Gruppe einem Benutzer mehrfach zugeordnet wird.

---

# 4. Tabelle `rechte`

## Beschreibung

Die Tabelle `rechte` enthält sämtliche verfügbaren Einzelberechtigungen der Anwendung.

Rechte beschreiben konkrete Fähigkeiten und werden niemals direkt über Gruppennamen geprüft.

## Beispiele

```text
benutzer_anzeigen
benutzer_bearbeiten
benutzer_gruppen_verwalten
agenten_anzeigen
agenten_starten
agenten_stoppen
logs_anzeigen
einstellungen_bearbeiten
api_logs_anzeigen
mcp_logs_anzeigen
```

## Vorgesehene Spalten

```text
id
key
bezeichnung
beschreibung
bereich
aktiv
erstellt_am
geaendert_am
```

## Hinweise

- `key` muss eindeutig sein.
- `bereich` dient zur Gruppierung in der Benutzeroberfläche.
- Rechte werden in der Anwendung ausschließlich über den `key` geprüft.

---

# 5. Tabelle `benutzer_gruppen_rechte`

## Beschreibung

Diese Tabelle ordnet Rechte den Benutzergruppen zu.

Damit ergibt sich folgende Berechtigungskette:

```text
Benutzer
    ↓
Benutzergruppen
    ↓
Rechte
```

## Vorgesehene Spalten

```text
id
benutzer_gruppe_id
recht_id
erstellt_von
erstellt_am
```

## Eindeutigkeit

Die Kombination aus:

```text
benutzer_gruppe_id
recht_id
```

muss eindeutig sein.

---

# 6. Tabelle `benutzer_log`

## Beschreibung

Die Tabelle `benutzer_log` protokolliert Aktivitäten eines angemeldeten Web-Benutzers.

Sie dient als Audit-Log für fachliche und administrative Änderungen.

## Beispiele für Aktivitäten

```text
Benutzer angelegt
Benutzer bearbeitet
Benutzer deaktiviert
Gruppe zugeordnet
Gruppe entfernt
Agent gestartet
Agent gestoppt
Einstellung geändert
Session beendet
Passwort geändert
```

## Vorgesehene Spalten

```text
id
benutzer_id
bereich
aktion
referenz_typ
referenz_id
beschreibung
daten_alt_json
daten_neu_json
ip_adresse
user_agent
request_id
erstellt_am
```

## Felderklärung

### `bereich`

Beispiele:

```text
benutzer
gruppen
rechte
agenten
einstellungen
sessions
system
```

### `aktion`

Beispiele:

```text
anlegen
anzeigen
bearbeiten
loeschen
aktivieren
deaktivieren
starten
stoppen
zuordnen
entfernen
```

### `referenz_typ`

Beschreibt die Art des betroffenen Datensatzes.

Beispiele:

```text
benutzer
gruppe
recht
agent
einstellung
session
```

### `referenz_id`

Enthält die ID des betroffenen Datensatzes.

Falls kein konkreter Datensatz betroffen ist:

```text
referenz_id = 0
```

### `daten_alt_json`

Enthält den Zustand vor einer Änderung.

### `daten_neu_json`

Enthält den Zustand nach einer Änderung.

Passwörter, Tokens, API-Schlüssel, MCP-Schlüssel und andere Geheimnisse dürfen niemals gespeichert werden.

---

# 7. Tabelle `benutzer_logins`

## Beschreibung

Die Tabelle `benutzer_logins` protokolliert ausschließlich Loginversuche der Weboberfläche.

Sie bleibt bewusst von `benutzer_log` getrennt, da Loginereignisse sicherheitsrelevant sind und separat ausgewertet werden sollen.

## Vorgesehene Spalten

```text
id
benutzer_id
benutzername_eingabe
erfolg
fehlergrund
ip_adresse
user_agent
session_id
request_id
erstellt_am
```

## Erfolgreicher Login

```text
benutzer_id = ID des Benutzers
erfolg = 1
fehlergrund = ''
```

## Fehlgeschlagener Login

Ist der Benutzer unbekannt:

```text
benutzer_id = 0
erfolg = 0
```

Der eingegebene Benutzername wird in:

```text
benutzername_eingabe
```

gespeichert.

## Mögliche Fehlergründe

```text
benutzer_unbekannt
passwort_falsch
benutzer_gesperrt
benutzer_inaktiv
rate_limit
session_fehler
```

---

# 8. Tabelle `sessions`

## Beschreibung

Die Tabelle `sessions` enthält aktive und beendete Web-Sessions.

Sie ermöglicht eine zentrale Verwaltung aller angemeldeten Geräte und Sitzungen.

## Vorgesehene Spalten

```text
id
session_id_hash
benutzer_id
ip_adresse
user_agent
daten_json
aktiv
erstellt_am
letzte_aktivitaet_am
gueltig_bis
beendet_am
```

## Funktionen

Die Tabelle ermöglicht:

- aktive Sessions anzeigen
- Geräte und Browser erkennen
- einzelne Sessions beenden
- alle Sessions eines Benutzers beenden
- Session-Ablauf kontrollieren
- verdächtige Sitzungen nachvollziehen
- eingeloggte Benutzer anzeigen

## Sicherheitsregel

Die tatsächliche PHP-Session-ID wird nicht im Klartext gespeichert.

Es wird ausschließlich ein Hash gespeichert.

---

# 9. Tabelle `api_benutzer`

## Beschreibung

Die Tabelle `api_benutzer` enthält ausschließlich Benutzer für den zentralen API-Endpunkt:

```text
/api.php
```

API-Benutzer besitzen keine Web-Session und kein normales Web-Passwort.

## Vorgesehene Spalten

```text
id
benutzername
bezeichnung
beschreibung
schluessel_id
schluessel_hash
berechtigungen_json
ip_beschraenkung_json
aktiv
gueltig_ab
gueltig_bis
letzte_verwendung_am
letzte_ip_adresse
erstellt_am
geaendert_am
```

## Zugangsdaten

Der API-Zugang besteht aus:

```text
schluessel_id
geheimer Schlüssel
```

Die `schluessel_id` ist eine öffentliche Kennung.

Der geheime Schlüssel wird nur einmal bei der Erstellung angezeigt.

In der Datenbank wird ausschließlich der Hash gespeichert.

## Berechtigungen

`berechtigungen_json` enthält die erlaubten API-Funktionen oder Module.

Die detaillierte Berechtigungslogik wird später separat beschrieben.

---

# 10. Tabelle `api_log`

## Beschreibung

Die Tabelle `api_log` protokolliert sämtliche Zugriffe auf:

```text
/api.php
```

## Vorgesehene Spalten

```text
id
api_benutzer_id
request_id
modul
aktion
http_methode
request_uri
parameter_json
antwort_json
http_status
erfolg
fehlermeldung
ip_adresse
user_agent
laufzeit_ms
erstellt_am
```

## Zweck

Die Tabelle beantwortet unter anderem:

- Welcher API-Benutzer hat zugegriffen?
- Welche Aktion wurde ausgeführt?
- Welche Parameter wurden übergeben?
- War der Request erfolgreich?
- Welcher HTTP-Status wurde zurückgegeben?
- Wie lange dauerte der Request?
- Von welcher IP-Adresse kam der Request?

## Sicherheitsregel

Folgende Inhalte dürfen niemals gespeichert werden:

```text
passwort
api_key
token
authorization
secret
mcp_key
```

Diese Werte müssen vor der Protokollierung entfernt oder maskiert werden.

---

# 11. Tabelle `mcp_benutzer`

## Beschreibung

Die Tabelle `mcp_benutzer` enthält ausschließlich Benutzer für den zentralen MCP-Endpunkt:

```text
/mcp.php
```

MCP-Benutzer besitzen eigene Zugangsdaten und eigene Berechtigungen.

## Vorgesehene Spalten

```text
id
benutzername
bezeichnung
beschreibung
schluessel_id
schluessel_hash
tools_json
resources_json
berechtigungen_json
ip_beschraenkkung_json
aktiv
gueltig_ab
gueltig_bis
letzte_verwendung_am
letzte_ip_adresse
erstellt_am
geaendert_am
```

## Trennung von API und MCP

Ein API-Schlüssel darf nicht automatisch für MCP verwendet werden.

Ein MCP-Schlüssel darf nicht automatisch für die API verwendet werden.

API- und MCP-Zugänge bleiben vollständig getrennt.

## Tools und Resources

Beispiele für `tools_json`:

```json
[
    "agent_starten",
    "agent_stoppen",
    "agent_status",
    "agent_nachricht_senden"
]
```

Beispiele für `resources_json`:

```json
[
    "agents",
    "systemstatus",
    "logs"
]
```

---

# 12. Tabelle `mcp_log`

## Beschreibung

Die Tabelle `mcp_log` protokolliert sämtliche Zugriffe auf:

```text
/mcp.php
```

## Vorgesehene Spalten

```text
id
mcp_benutzer_id
request_id
methode
tool
resource
parameter_json
antwort_json
erfolg
fehlermeldung
ip_adresse
client_name
client_version
laufzeit_ms
erstellt_am
```

## Mögliche Methoden

```text
initialize
tools/list
tools/call
resources/list
resources/read
prompts/list
prompts/get
```

## Zweck

Die Tabelle dient zur Nachvollziehbarkeit von:

- MCP-Initialisierungen
- Tool-Aufrufen
- Resource-Aufrufen
- fehlenden Rechten
- ungültigen Anfragen
- Protokollfehlern
- Verarbeitungszeiten

---

# 13. Tabelle `agents`

## Beschreibung

Die Tabelle `agents` enthält sowohl Agent-Server als auch Sub-Agenten.

Ein Agent-Server ist ein Hauptdatensatz.

Ein Sub-Agent ist einem Agent-Server zugeordnet.

## Vorgesehene Spalten

```text
id
haupt_id
typ
bezeichnung
beschreibung
hostname
ip_adresse
port
protokoll
basis_url
status
aktiv
konfiguration_json
letzter_status_am
letzte_aktivitaet_am
erstellt_am
geaendert_am
```

## Haupt-Agent beziehungsweise Server

```text
id = 10
haupt_id = 0
typ = server
```

Der Wert:

```text
haupt_id = 0
```

bedeutet, dass es sich um einen eigenständigen Agent-Server handelt.

## Sub-Agent

```text
id = 11
haupt_id = 10
typ = sub_agent
```

Der Wert in `haupt_id` verweist auf den übergeordneten Agent-Server.

## Beispiel

| id | haupt_id | typ | bezeichnung |
|---:|---:|---|---|
| 1 | 0 | server | OpenClaw Server |
| 2 | 1 | sub_agent | Coding Agent |
| 3 | 1 | sub_agent | Recherche Agent |
| 4 | 0 | server | Hermes Server |
| 5 | 4 | sub_agent | Support Agent |

## Funktionsweise

Die Struktur ermöglicht:

```text
Agent-Server
├── Sub-Agent 1
├── Sub-Agent 2
└── Sub-Agent 3
```

## Wichtige Regel

Da `haupt_id = 0` zulässig ist, wird die Beziehung nicht als normaler Fremdschlüssel mit `NULL` umgesetzt.

Die Gültigkeit von `haupt_id` wird durch zentrale Business-Logik geprüft.

---

# 14. Tabelle `agents_log`

## Beschreibung

Die Tabelle `agents_log` protokolliert Aktivitäten und Statusänderungen der Agenten.

Sie dokumentiert das tatsächliche Verhalten eines Agenten unabhängig davon, ob die Aktion über Web, API, MCP oder das System ausgelöst wurde.

## Vorgesehene Spalten

```text
id
agent_id
benutzer_id
api_benutzer_id
mcp_benutzer_id
quelle
aktion
status
nachricht
daten_json
laufzeit_ms
erstellt_am
```

## Mögliche Quellen

```text
web
api
mcp
system
agent
```

## Mögliche Aktionen

```text
starten
stoppen
neustarten
status_pruefen
nachricht_senden
konfiguration_aendern
fehler
```

## Nicht vorhandene Benutzerzuordnung

Ist keine Benutzerzuordnung vorhanden:

```text
benutzer_id = 0
api_benutzer_id = 0
mcp_benutzer_id = 0
```

---

# 15. Tabelle `db_fehler_log`

## Beschreibung

Die Tabelle `db_fehler_log` protokolliert ausschließlich Fehler im Zusammenhang mit MariaDB oder SQL.

## Vorgesehene Spalten

```text
id
benutzer_id
api_benutzer_id
mcp_benutzer_id
agent_id
quelle
datei
funktion
zeile
sql_status
sql_code
fehlermeldung
sql_befehl
parameter_json
request_id
ip_adresse
erstellt_am
```

## Typische Fehler

```text
Verbindungsfehler
ungültige SQL-Abfrage
fehlende Tabelle
fehlende Spalte
Unique-Verletzung
Fremdschlüsselfehler
Deadlock
Timeout
ungültiger Datentyp
```

## Ablauf

```text
SQL-Fehler tritt auf
        ↓
Exception wird abgefangen
        ↓
Fehler wird in db_fehler_log gespeichert
        ↓
Benutzer erhält eine neutrale Fehlermeldung
```

## Sicherheitsregel

Folgende Inhalte müssen maskiert werden:

```text
passwort
token
api_key
mcp_key
secret
authorization
```

Der Benutzer darf niemals die vollständige SQL-Fehlermeldung sehen.

---

# 16. Tabelle `system_log`

## Beschreibung

Die Tabelle `system_log` protokolliert technische Ereignisse und Fehler, die keine direkten SQL-Fehler sind.

Sie ergänzt `db_fehler_log`.

## Typische Ereignisse

```text
PHP-Exception
API-Verbindungsfehler
MCP-Protokollfehler
Dateisystemfehler
fehlgeschlagener Cronjob
Konfigurationsfehler
Agent nicht erreichbar
Berechtigungsproblem
Debugmeldung
Systemstart
Systemstopp
```

## Vorgesehene Spalten

```text
id
benutzer_id
api_benutzer_id
mcp_benutzer_id
agent_id
stufe
bereich
quelle
meldung
details_json
datei
funktion
zeile
request_id
erstellt_am
```

## Mögliche Stufen

```text
info
warnung
fehler
kritisch
debug
```

---

# 17. Tabelle `einstellungen`

## Beschreibung

Die Tabelle `einstellungen` ist die zentrale Key-Value-Ablage für globale und benutzerspezifische Einstellungen.

Sie soll flexibel genug sein, um einfache Werte, lange Texte und JSON-Konfigurationen zu speichern.

## Vorgesehene Spalten

```text
id
benutzer
key
wert
datentyp
beschreibung
erstellt_am
geaendert_am
```

## Feld `benutzer`

Das Feld `benutzer` bestimmt den Gültigkeitsbereich.

Beispiele:

```text
system
benutzer:15
api:3
mcp:7
agent:12
```

### Globaler Wert

```text
benutzer = system
```

Der Wert gilt global für die gesamte Anwendung.

### Benutzerspezifischer Wert

```text
benutzer = benutzer:15
```

Der Wert gilt nur für Benutzer 15.

### API-spezifischer Wert

```text
benutzer = api:3
```

Der Wert gilt nur für API-Benutzer 3.

### MCP-spezifischer Wert

```text
benutzer = mcp:7
```

Der Wert gilt nur für MCP-Benutzer 7.

### Agentenspezifischer Wert

```text
benutzer = agent:12
```

Der Wert gilt nur für Agent 12.

## Feld `key`

Der Schlüssel identifiziert die Einstellung.

Beispiele:

```text
system.name
system.debug
design.modus
dashboard.widgets
api.rate_limit
mcp.logging
agent.standard_modell
```

## Feld `wert`

Der Wert wird als:

```text
LONGTEXT
```

gespeichert.

Dadurch kann das Feld enthalten:

- Text
- Zahlen
- boolesche Werte
- lange Texte
- JSON
- Arrays
- komplexe Konfigurationen

## Feld `datentyp`

Mögliche Werte:

```text
string
integer
decimal
boolean
json
text
```

## Eindeutigkeit

Die Kombination aus:

```text
benutzer
key
```

muss eindeutig sein.

Ein Gültigkeitsbereich darf denselben Schlüssel nur einmal besitzen.

## Detaillierte Funktionsweise

Die genaue Lade-, Speicher-, Fallback- und Validierungslogik wird später in Verbindung mit folgender Datei beschrieben:

```text
/inc/funktionen_einstellungen.php
```

Dieses Dokument definiert zunächst ausschließlich die Tabelle und ihren grundsätzlichen Zweck.

---

# Verbindliche Regel zu `NULL`

In der gesamten Core-Datenbank wird grundsätzlich kein `NULL` verwendet.

## Standardwerte

| Datentyp | Standardwert |
|---|---:|
| ID / Integer | `0` |
| Boolean | `0` |
| Dezimalzahl | `0.00` |
| VARCHAR / TEXT | `''` |
| JSON-Objekt | `'{}'` |
| JSON-Liste | `'[]'` |
| Zeitangabe | `0` |

## Zeitangaben

Zeitangaben werden als Unix-Timestamp gespeichert:

```sql
BIGINT UNSIGNED NOT NULL DEFAULT 0
```

Beispiel:

```text
erstellt_am = 1784192400
```

Der Wert:

```text
0
```

bedeutet:

```text
nicht gesetzt
```

Damit werden problematische MariaDB-Zero-Dates wie:

```text
0000-00-00 00:00:00
```

vermieden.

---

# Zusammenfassung

Die Core-Datenbank besteht aus folgenden Tabellen:

```text
benutzer
benutzer_gruppen
benutzer_gruppen_zuordnung
rechte
benutzer_gruppen_rechte

benutzer_log
benutzer_logins
sessions

api_benutzer
api_log

mcp_benutzer
mcp_log

agents
agents_log

db_fehler_log
system_log

einstellungen
```

Damit sind folgende Kernbereiche abgedeckt:

- Benutzerverwaltung
- Gruppen und Rechte
- Aktivitätsprotokolle
- Loginprotokolle
- Web-Sessions
- API-Zugänge
- MCP-Zugänge
- Agenten und Sub-Agenten
- Agentenaktivitäten
- SQL-Fehler
- Systemfehler
- globale und benutzerspezifische Einstellungen