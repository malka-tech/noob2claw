# 1_MCP_Defintion.md

# Noob2Claw – Folge 13: MCP-Funktionen für das Wiki

Version: 1.0  
Bereich: Folge 13  
Zielsystem: Noob2Claw Core + bestehendes Wiki + OpenClaw-Agent  
Technologien: PHP, MariaDB, Apache, MCP, OpenClaw, JSON-RPC, Markdown, Datei-Upload

---

# Ziel

In Folge 12 wurde ein eigenes Wiki für Noob2Claw aufgebaut.

In Folge 13 wird dieses Wiki für KI-Agenten nutzbar gemacht.

Der bestehende Noob2Claw-MCP-Server soll so erweitert werden, dass ein OpenClaw-Agent:

- Wiki-Kategorien lesen kann
- Wiki-Artikel suchen kann
- Wiki-Artikel lesen kann
- Wiki-Artikel erstellen kann
- Wiki-Artikel bearbeiten kann
- Dateien an Wiki-Artikel anhängen kann
- Wiki-Versionen prüfen kann
- sauber als Agent in der Änderungshistorie erscheint

Wichtig:

```text
Der Agent arbeitet nicht anonym im Wiki.
```

Jede Änderung über MCP muss später nachvollziehbar sein.

---

# Ausgangspunkt

Es gibt bereits:

```text
Noob2Claw Core
├── index.php
├── api.php
├── mcp.php
├── inc/
├── nav/
├── css/
├── js/
└── uploads/
```

Es gibt bereits einen MCP-Server:

```text
/mcp.php
```

Dieser bleibt der einzige öffentliche MCP-Endpunkt.

Es darf kein zweiter MCP-Endpunkt entstehen.

Richtig:

```text
https://domain.tld/mcp.php
```

Falsch:

```text
https://domain.tld/wiki_mcp.php
https://domain.tld/mcp/wiki.php
https://domain.tld/api/wiki_mcp.php
```

---

# Grundprinzip

Die Architektur bleibt sauber getrennt.

```text
OpenClaw-Agent
  │
  ▼
Noob2Claw MCP-Server
  │
  ▼
mcp.php
  │
  ▼
MCP-Authentifizierung
  │
  ▼
MCP-Rechteprüfung
  │
  ▼
funktionen_mcp_wiki.php
  │
  ▼
vorhandene Wiki-Business-Funktionen
  │
  ▼
MariaDB
```

`mcp.php` macht nur Protokoll, Auth, Routing und Antwortformat.

`funktionen_mcp_wiki.php` kennt die Wiki-MCP-Tools und leitet auf zentrale Wiki-Funktionen weiter.

Die eigentliche Wiki-Logik bleibt in den vorhandenen Wiki-Funktionsdateien.

---

# Was ausdrücklich nicht gebaut wird

Nicht Bestandteil dieser Folge:

- ein zweites Wiki
- ein separater Wiki-MCP-Endpunkt
- ein neues Benutzer- oder Rechtesystem
- ein neues Upload-System nur für MCP
- direkter SQL-Code in `mcp.php`
- direkter SQL-Code in `nav/`
- öffentliche Datei-Downloads ohne Rechteprüfung
- anonyme Schreibzugriffe durch Agenten
- Tokenübergabe per URL
- automatische Veröffentlichung ohne Rechteprüfung
- ungeprüfte HTML-Ausgabe aus Markdown

---

# Begriffsklärung

| Begriff | Bedeutung |
|---|---|
| MCP-Zugang | Technischer Zugang zum Noob2Claw-MCP-Server |
| MCP-Token | Geheimer Schlüssel zur Authentifizierung |
| Agent-ID | ID des Agenten aus der Agentenverwaltung |
| Token-Suffix | Zusatz `:AGENT_ID` hinter dem Token |
| Wiki-Tool | MCP-Tool für eine Wiki-Aktion |
| Wiki-Version | Änderungshistorie eines Wiki-Artikels |
| Quelle | Ursprung einer Änderung, z. B. `web`, `api`, `mcp`, `system` |
| Akteur | Benutzer, MCP-Zugang oder Agent, der eine Änderung ausgelöst hat |

---

# MCP-Masken im Webinterface

## Ziel

Die MCP-Verwaltung muss so sauber sein, dass im Video echte Zugangsdaten erzeugt werden können.

Der Benutzer soll im Webinterface einen MCP-Zugang erstellen, Tools erlauben, Agenten zuordnen und danach die Konfiguration für OpenClaw kopieren können.

## Pflichtmasken

Mindestens prüfen oder erstellen:

```text
nav/settings_mcp_benutzer.php
nav/control_mcp_log.php
```

Optional, wenn sinnvoll getrennt:

```text
nav/settings_mcp_tools.php
nav/settings_mcp_resources.php
nav/settings_mcp_agenten.php
```

## Navigationsmetadaten

Beispiel:

```php
<?php
// TITLE: MCP-Zugänge
// ICON: key
// BEREICH: Settings
// PRIO: 60
// RECHT: mcp_benutzer_anzeigen
?>
```

```php
<?php
// TITLE: MCP-Log
// ICON: terminal
// BEREICH: Control
// PRIO: 60
// RECHT: mcp_log_anzeigen
?>
```

## MCP-Zugangsliste

Die Liste zeigt mindestens:

- Bezeichnung
- Benutzername oder Schlüssel-ID
- aktiv
- gesperrt
- erlaubte Tools
- erlaubte Resources
- erlaubte Agenten
- letzte Verwendung
- letzte IP
- Gültigkeit
- Aktionen

Nicht anzeigen:

- vollständiger Token
- vollständiger Hash
- Authorization-Header mit echtem Token nach dem Erstellmoment

## MCP-Zugang bearbeiten

Pflichtfelder:

```text
bezeichnung
beschreibung
aktiv
gesperrt
gueltig_ab
gueltig_bis
ip_beschraenkung_json
tools_json
resources_json
berechtigungen_json
erlaubte_agenten
```

Die UI muss verständlich trennen:

```text
MCP-Zugang
├── Authentifizierung
├── Tools
├── Resources
├── Rechte
└── erlaubte Agenten
```

## Token erzeugen

Beim Erzeugen oder Neu-Erzeugen wird der Klartext-Token nur einmal angezeigt.

Die Maske zeigt direkt:

```text
MCP-URL
MCP-Token
Agent-ID
Authorization-Header
OpenClaw-Konfigurationsblock
Testbefehl
```

Danach wird nur noch angezeigt:

```text
Token wurde erzeugt.
Er kann nicht erneut angezeigt werden.
Bei Verlust bitte neu erzeugen.
```

---

# Authentifizierung mit Agent-ID im Token-Suffix

## Problem

Bisher weiß das System nur:

```text
Welcher MCP-Zugang hat etwas gemacht?
```

Für echte Agentenarbeit reicht das nicht.

Wir müssen zusätzlich wissen:

```text
Welcher Agent hat etwas gemacht?
```

## Lösung

Der Bearer Token darf um eine Agent-ID erweitert werden.

Format:

```http
Authorization: Bearer MCP_TOKEN:AGENT_ID
```

Beispiel:

```http
Authorization: Bearer n2c_mcp_XYZ123456789:17
```

## Bedeutung

```text
n2c_mcp_XYZ123456789 = echter MCP-Token
17                    = Agent-ID
```

Wichtig:

```text
Die Agent-ID ist keine Authentifizierung.
```

Die Agent-ID dient nur für:

- Zuordnung
- Rechtebegrenzung
- Audit
- Versionierung
- Anzeige im Wiki
- Logauswertung

## Rückwärtskompatibilität

Bestehende Tokens ohne Agent-ID bleiben gültig:

```http
Authorization: Bearer MCP_TOKEN
```

Aber:

- read-only Tools können damit erlaubt sein
- schreibende Wiki-Tools sollen eine Agent-ID verlangen
- diese Regel kann später pro Tool konfigurierbar gemacht werden

---

# Token-Parsing

Die Tokenverarbeitung erfolgt zentral in der MCP-Authentifizierung.

Nicht in jedem Tool.

Grundlogik:

```php
$authorization = mcp_authorization_header_laden();
$bearer = mcp_bearer_token_extrahieren($authorization);

$token = $bearer;
$agent_id = 0;

$pos = strrpos($bearer, ':');

if ($pos !== false) {
    $suffix = substr($bearer, $pos + 1);
    $basis_token = substr($bearer, 0, $pos);

    if ($suffix !== '' && ctype_digit($suffix)) {
        $token = $basis_token;
        $agent_id = (int)$suffix;
    }
}

$mcp_benutzer = mcp_benutzer_authentifizieren($token);
```

Regeln:

- Auf den **letzten** Doppelpunkt splitten.
- Nur numerische Suffixe als Agent-ID akzeptieren.
- Der Token ohne Suffix wird wie bisher authentifiziert.
- Ungültiger Suffix führt nicht zu stiller Rechteausweitung.
- Tokens werden nie geloggt.
- Authorization-Header werden maskiert.

---

# Agent-ID validieren

Nach erfolgreicher MCP-Authentifizierung:

```php
if ($agent_id > 0) {
    $agent = agent_laden($agent_id);

    if (!$agent) {
        return fehler('Agent existiert nicht.');
    }

    if ((int)$agent['aktiv'] !== 1) {
        return fehler('Agent ist nicht aktiv.');
    }

    if (agent_ist_geloescht($agent)) {
        return fehler('Agent ist gelöscht.');
    }

    if (!mcp_benutzer_darf_agent_nutzen($mcp_benutzer['id'], $agent_id)) {
        return fehler('MCP-Zugang darf diesen Agenten nicht verwenden.');
    }
}
```

Für Schreibtools zusätzlich:

```php
if ($agent_id <= 0) {
    return fehler('Für schreibende Wiki-Tools ist eine Agent-ID erforderlich.');
}

if (!mcp_benutzer_darf_agent_schreiben($mcp_benutzer_id, $agent_id)) {
    return fehler('Agent besitzt kein Schreibrecht über diesen MCP-Zugang.');
}
```

---

# MCP-Kontext

Nach Authentifizierung steht überall ein zentraler Kontext zur Verfügung.

Beispiel:

```php
$mcp_kontext = [
    'mcp_benutzer_id' => 12,
    'mcp_benutzer_bezeichnung' => 'OpenClaw Wiki Access',
    'agent_id' => 17,
    'agent_bezeichnung' => 'Claudia Wiki',
    'quelle' => 'mcp',
    'request_id' => 'req_...',
    'ip_adresse' => '192.168.178.10',
    'client_name' => 'OpenClaw',
    'client_version' => '',
];
```

Dieser Kontext wird an Wiki-Business-Funktionen übergeben.

Nicht als globales Chaos.

Sinnvoll:

```php
wiki_artikel_speichern($daten, $mcp_kontext);
```

Nicht sinnvoll:

```php
$GLOBALS['agent_id'] = $_GET['agent_id'];
```

---

# Datenbankstruktur

## Grundregel

Vorhandene Tabellen zuerst prüfen.

Keine doppelten Tabellen.

Keine destruktiven Änderungen.

Keine `NULL`-Werte.

## Neue Tabelle `mcp_benutzer_agents`

Diese Tabelle ist sinnvoll, wenn noch keine saubere Zuordnung zwischen MCP-Zugang und Agenten existiert.

Aufgabe:

```text
Welcher MCP-Zugang darf welchen Agenten verwenden?
```

Felder:

```text
id
mcp_benutzer_id
agent_id
lesen_erlaubt
schreiben_erlaubt
aktiv
erstellt_am
geaendert_am
```

SQL-Konzept:

```sql
CREATE TABLE IF NOT EXISTS mcp_benutzer_agents (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    mcp_benutzer_id BIGINT UNSIGNED NOT NULL DEFAULT 0,
    agent_id BIGINT UNSIGNED NOT NULL DEFAULT 0,
    lesen_erlaubt TINYINT(1) NOT NULL DEFAULT 1,
    schreiben_erlaubt TINYINT(1) NOT NULL DEFAULT 0,
    aktiv TINYINT(1) NOT NULL DEFAULT 1,
    erstellt_am BIGINT UNSIGNED NOT NULL DEFAULT 0,
    geaendert_am BIGINT UNSIGNED NOT NULL DEFAULT 0,
    PRIMARY KEY (id),
    UNIQUE KEY uniq_mcp_agent (mcp_benutzer_id, agent_id),
    KEY idx_mcp_benutzer_id (mcp_benutzer_id),
    KEY idx_agent_id (agent_id),
    KEY idx_aktiv (aktiv)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

Wenn das bestehende Projekt bereits eine andere Zuordnung sauber abbildet, diese verwenden und nicht doppelt bauen.

## Erweiterung `mcp_log`

Falls noch nicht vorhanden:

```text
agent_id
```

Ziel:

```text
MCP-Logs nach Agent filtern können.
```

## Erweiterung der Wiki-Versionstabelle

Die konkrete Tabelle hängt von Folge 12 ab.

Typische Namen:

```text
wiki_versionen
wiki_artikel_versionen
wiki_artikel_log
```

Die Versionierung muss mindestens abbilden:

```text
quelle
benutzer_id
api_benutzer_id
mcp_benutzer_id
agent_id
akteur_typ
akteur_name
request_id
aenderungsgrund
```

Regel:

- `akteur_name` ist ein Snapshot.
- Dadurch bleibt die Version verständlich, auch wenn der Agent später umbenannt wird.

---

# Funktionsdateien

## Neue oder erweiterte Dateien

```text
inc/funktionen_mcp_wiki.php
inc/funktionen_mcp_benutzer_agents.php
```

Falls noch nicht vorhanden oder unvollständig:

```text
inc/funktionen_mcp_benutzer.php
inc/funktionen_mcp_log.php
inc/funktionen_wiki.php
inc/funktionen_wiki_kategorien.php
inc/funktionen_wiki_artikel.php
inc/funktionen_wiki_dateien.php
inc/funktionen_wiki_versionen.php
```

## `funktionen_mcp_wiki.php`

Diese Datei enthält:

```php
mcp_wiki_tools_array()
mcp_wiki_tool_ausfuehren()
mcp_wiki_tool_recht()
mcp_wiki_tool_schreibend()
mcp_wiki_tool_parameter_schema()
```

Sie enthält keine SQL-Abfragen.

Sie ruft zentrale Wiki-Funktionen auf.

## `funktionen_mcp_benutzer_agents.php`

Diese Datei verarbeitet ausschließlich die Tabelle:

```text
mcp_benutzer_agents
```

Pflichtfunktionen:

```php
mcp_benutzer_agents_array()
mcp_benutzer_agent_laden()
mcp_benutzer_agent_speichern()
mcp_benutzer_agent_loeschen()
mcp_benutzer_agent_existiert()
mcp_benutzer_darf_agent_nutzen()
mcp_benutzer_darf_agent_lesen()
mcp_benutzer_darf_agent_schreiben()
```

---

# MCP-Wiki-Tools

## Übersicht

| Tool | Zweck | Schreibend | Agent-ID erforderlich |
|---|---|---:|---:|
| `wiki_artikel_suchen` | Artikel suchen | nein | nein |
| `wiki_artikel_laden` | Artikel laden | nein | nein |
| `wiki_artikel_anlegen` | neuen Artikel erstellen | ja | ja |
| `wiki_artikel_bearbeiten` | Artikel ändern | ja | ja |
| `wiki_artikel_veroeffentlichen` | Artikel veröffentlichen | ja | ja |
| `wiki_artikel_archivieren` | Artikel archivieren | ja | ja |
| `wiki_kategorien_liste` | Kategorien auflisten | nein | nein |
| `wiki_kategorie_laden` | Kategorie laden | nein | nein |
| `wiki_kategorie_anlegen` | Kategorie erstellen | ja | ja |
| `wiki_kategorie_bearbeiten` | Kategorie ändern | ja | ja |
| `wiki_dateien_liste` | Dateien eines Artikels anzeigen | nein | nein |
| `wiki_datei_hochladen` | Datei anhängen | ja | ja |
| `wiki_datei_laden` | Datei-Metadaten oder Inhalt laden | nein | nein |
| `wiki_versionen_liste` | Versionen eines Artikels anzeigen | nein | nein |
| `wiki_version_laden` | konkrete Version anzeigen | nein | nein |

---

# Tool-Definitionen

## `wiki_artikel_suchen`

Zweck:

```text
Findet Wiki-Artikel anhand von Suchbegriff, Kategorie, Status oder Schlagwort.
```

Parameter:

```json
{
  "suchbegriff": "string",
  "kategorie_id": 0,
  "status": "veroeffentlicht",
  "limit": 20
}
```

Antwortdaten:

```json
{
  "artikel": [
    {
      "id": 12,
      "titel": "Installation",
      "kategorie_id": 3,
      "status": "veroeffentlicht",
      "kurzbeschreibung": "...",
      "geaendert_am": 1784630000
    }
  ]
}
```

## `wiki_artikel_laden`

Parameter:

```json
{
  "artikel_id": 12,
  "version_id": 0,
  "include_markdown": 1,
  "include_dateien": 1
}
```

## `wiki_artikel_anlegen`

Parameter:

```json
{
  "titel": "MCP Test durch OpenClaw",
  "kategorie_id": 3,
  "kurzbeschreibung": "Testartikel",
  "inhalt_markdown": "# Test\n\nDieser Artikel wurde über MCP erstellt.",
  "status": "entwurf",
  "aenderungsgrund": "MCP-Test Folge 13"
}
```

Regeln:

- Titel Pflicht
- Kategorie Pflicht, falls Wiki dies verlangt
- Markdown sicher speichern
- Version erzeugen
- Agent im Versionsdatensatz speichern
- Entwurf als Standard, wenn nichts anderes erlaubt ist

## `wiki_artikel_bearbeiten`

Parameter:

```json
{
  "artikel_id": 12,
  "titel": "MCP Test durch OpenClaw",
  "kategorie_id": 3,
  "kurzbeschreibung": "aktualisiert",
  "inhalt_markdown": "# Aktualisiert\n\nNeue Version.",
  "status": "entwurf",
  "aenderungsgrund": "MCP-Bearbeitung durch Agent"
}
```

Regeln:

- Artikel muss existieren
- Rechte prüfen
- Agent-Schreibrecht prüfen
- neue Version erzeugen
- alte Version nicht überschreiben

## `wiki_datei_hochladen`

Parameter:

```json
{
  "artikel_id": 12,
  "dateiname": "screenshot.png",
  "mime_type": "image/png",
  "inhalt_base64": "...",
  "beschreibung": "Screenshot zur Installation"
}
```

Regeln:

- maximale Dateigröße aus Einstellung laden
- MIME-Type serverseitig prüfen
- Dateiendung prüfen
- sicheren Dateinamen erzeugen
- Datei nicht ausführbar ablegen
- Zugriff nur über gesicherte Auslieferung
- Upload als Wiki-Version oder Wiki-Datei-Historie protokollieren
- Agent im Audit speichern

## `wiki_versionen_liste`

Parameter:

```json
{
  "artikel_id": 12,
  "limit": 20
}
```

Antwort enthält:

```json
{
  "versionen": [
    {
      "id": 55,
      "artikel_id": 12,
      "quelle": "mcp",
      "akteur_typ": "agent",
      "akteur_name": "Claudia Wiki",
      "mcp_benutzer_id": 4,
      "agent_id": 17,
      "aenderungsgrund": "MCP-Test Folge 13",
      "erstellt_am": 1784630000
    }
  ]
}
```

---

# Antwortformat der Tool-Funktionen

Die MCP-Wiki-Funktionen geben ein neutrales Ergebnisarray zurück.

Beispiel:

```php
return [
    'erfolg' => true,
    'meldung' => '',
    'daten' => [
        'artikel_id' => 12,
    ],
];
```

`mcp.php` wandelt dieses Ergebnis anschließend in die korrekte MCP-Protokollantwort um.

Nicht in `funktionen_mcp_wiki.php`:

```php
echo json_encode(...);
```

---

# Rechtekonzept

## Web-Rechte für Masken

```text
mcp_benutzer_anzeigen
mcp_benutzer_verwalten
mcp_benutzer_token_erzeugen
mcp_benutzer_agents_verwalten
mcp_tools_verwalten
mcp_resources_verwalten
mcp_log_anzeigen
mcp_testen
```

## MCP-Rechte für Wiki

```text
wiki_mcp_lesen
wiki_mcp_schreiben
wiki_mcp_kategorien_verwalten
wiki_mcp_dateien_hochladen
wiki_mcp_versionen_anzeigen
```

## Wiki-Rechte wiederverwenden

Wenn bereits Wiki-Rechte existieren, diese nicht doppelt anlegen.

Typische vorhandene Rechte:

```text
wiki_anzeigen
wiki_artikel_anzeigen
wiki_artikel_anlegen
wiki_artikel_bearbeiten
wiki_artikel_veroeffentlichen
wiki_artikel_archivieren
wiki_kategorien_verwalten
wiki_dateien_hochladen
wiki_versionen_anzeigen
```

Regel:

```text
MCP-Zugang muss Tool-Recht besitzen.
Agent muss für diesen Zugang erlaubt sein.
Wiki-Funktion prüft zusätzlich das fachliche Wiki-Recht.
```

---

# Versionierung im Wiki

## Ziel

Bei jeder Änderung muss später sichtbar sein:

```text
Wer hat geändert?
Über welchen Zugang?
War es ein Mensch oder ein Agent?
```

## Anzeige in der Versionenliste

Beispiele:

```text
Version 7
Geändert durch Agent: Claudia Wiki
Quelle: MCP
MCP-Zugang: OpenClaw Wiki Access
Änderungsgrund: Struktur ergänzt
Zeitpunkt: 21.07.2026 14:22
```

```text
Version 8
Geändert durch Benutzer: Mario
Quelle: Web
Änderungsgrund: manuelle Korrektur
Zeitpunkt: 21.07.2026 14:31
```

## UI-Elemente

Die Versionenansicht soll mit klaren Badges arbeiten:

```text
[MCP] [Agent] Claudia Wiki
[Web] [Benutzer] Mario
[System] Migration
```

Nicht anzeigen:

- Token
- Hash
- vollständiger Authorization-Header
- geheime Header

---

# MCP-Logging

Jeder MCP-Zugriff wird protokolliert.

Ergänzende Logfelder:

```text
mcp_benutzer_id
agent_id
methode
tool
resource
erfolg
fehlermeldung
laufzeit_ms
ip_adresse
client_name
client_version
request_id
```

Parameter und Antworten müssen maskiert werden.

Zu maskieren:

```text
token
authorization
secret
password
api_key
mcp_key
schluessel
inhalt_base64 bei großen Dateien
```

Bei Uploads nicht den vollständigen Base64-Inhalt loggen.

Stattdessen:

```text
dateiname
mime_type
groesse
sha256
```

---

# OpenClaw-Konfiguration

## Ziel

Der OpenClaw-Agent soll die MCP-Zugangsdaten selbst in seine Konfiguration eintragen.

Pfad:

```text
~/.openclaw/openclaw.json
```

## Sicherer Ablauf

1. Datei prüfen
2. Backup erstellen
3. JSON/JSON5 sauber laden
4. bestehenden `mcp.servers`-Block erhalten
5. `noob2claw_wiki` ergänzen oder aktualisieren
6. keine anderen Server löschen
7. Datei speichern
8. OpenClaw MCP-Status prüfen
9. Probe ausführen

## Beispielkonfiguration

```json
{
  "mcp": {
    "servers": {
      "noob2claw_wiki": {
        "url": "https://DEINE-DOMAIN/mcp.php",
        "transport": "streamable-http",
        "headers": {
          "Authorization": "Bearer MCP_TOKEN:AGENT_ID"
        },
        "requestTimeoutMs": 30000,
        "connectionTimeoutMs": 10000,
        "supportsParallelToolCalls": false
      }
    }
  }
}
```

## Prüfung

```bash
openclaw mcp status --verbose
openclaw mcp doctor noob2claw_wiki --probe
openclaw mcp probe noob2claw_wiki --json
```

Wenn die lokale OpenClaw-Version andere Befehle benötigt, soll der Agent die verfügbare CLI-Hilfe nutzen:

```bash
openclaw mcp --help
openclaw mcp status --help
openclaw mcp doctor --help
openclaw mcp probe --help
```

---

# Beispielprompt für den OpenClaw-Agenten

Dieser Prompt wird nach Erstellung des MCP-Zugangs an den OpenClaw-Agenten gegeben.

```text
Du bist mein OpenClaw-Agent auf diesem System.

Richte folgenden Noob2Claw-MCP-Server in deiner OpenClaw-Konfiguration ein.

Wichtig:
- Bearbeite ~/.openclaw/openclaw.json vorsichtig.
- Erstelle vorher ein Backup.
- Lösche keine bestehenden MCP-Server.
- Trage den Server unter mcp.servers.noob2claw_wiki ein.
- Verwende den Authorization-Header exakt wie angegeben.
- Prüfe danach mit openclaw mcp status, doctor und probe.
- Führe anschließend einen Testaufruf an das Wiki aus.

MCP-Server:
Name: noob2claw_wiki
URL: https://DEINE-DOMAIN/mcp.php
Transport: streamable-http
Authorization: Bearer MCP_TOKEN:AGENT_ID

Teste danach:
1. Tools auflisten
2. Wiki-Kategorien lesen
3. Wiki-Artikel suchen
4. einen Testartikel als Entwurf anlegen
5. Versionen des Testartikels lesen
6. bestätigen, dass dein Agentenname in der Wiki-Version sichtbar ist

Wenn ein Schritt fehlschlägt, brich nicht blind ab.
Analysiere die Ursache, behebe sie wenn möglich und dokumentiere das Ergebnis.
```

---

# Testfälle

## Authentifizierung

| Fall | Erwartung |
|---|---|
| gültiger Token ohne Agent-ID, read-only Tool | erlaubt, wenn Recht vorhanden |
| gültiger Token ohne Agent-ID, Schreibtool | abgelehnt |
| gültiger Token mit erlaubter Agent-ID | erlaubt |
| gültiger Token mit nicht erlaubter Agent-ID | abgelehnt |
| gültiger Token mit deaktiviertem Agenten | abgelehnt |
| ungültiger Token mit Agent-ID | abgelehnt |
| Token in URL | abgelehnt oder ignoriert |

## Wiki-Tools

| Tool | Test |
|---|---|
| `wiki_artikel_suchen` | findet vorhandene Artikel |
| `wiki_artikel_laden` | liefert Markdown und Metadaten |
| `wiki_artikel_anlegen` | erstellt Artikel mit Version |
| `wiki_artikel_bearbeiten` | erzeugt neue Version |
| `wiki_datei_hochladen` | lädt erlaubte Datei hoch |
| `wiki_versionen_liste` | zeigt Agentenänderung |

## Sicherheit

| Test | Erwartung |
|---|---|
| Upload `.php` | abgelehnt |
| Upload zu groß | abgelehnt |
| Base64 ungültig | abgelehnt |
| Kategorie ohne Recht | abgelehnt |
| Artikel ohne Recht | abgelehnt |
| Authorization im Log | maskiert |

---

# Akzeptanzkriterien

Folge 13 ist fertig, wenn:

- alle MCP-Masken sauber funktionieren
- MCP-Zugänge angelegt werden können
- MCP-Tools und Rechte verwaltet werden können
- MCP-Zugang erlaubten Agenten zugeordnet werden kann
- `Authorization: Bearer TOKEN:AGENT_ID` zentral verarbeitet wird
- Wiki-MCP-Tools über `mcp.php` verfügbar sind
- schreibende Tools eine gültige Agent-ID verlangen
- Wiki-Versionen den Agenten anzeigen
- MCP-Log die Agent-ID speichert
- OpenClaw den MCP-Server in `openclaw.json` eingerichtet hat
- `openclaw mcp probe` die Wiki-Tools sieht
- ein Testartikel über MCP erstellt oder bearbeitet wurde
- die Version im Wiki den Agenten zeigt
- alle PHP-Dateien syntaktisch gültig sind
- keine Tokens im Klartext gespeichert oder geloggt werden

---

# Wichtigste Aussage für das Video

Vorher:

```text
Das Wiki ist eine Wissensdatenbank für Menschen.
```

Nachher:

```text
Das Wiki ist eine kontrollierte Wissensschnittstelle für Menschen und KI-Agenten.
```

Der entscheidende Punkt ist nicht nur MCP.

Der entscheidende Punkt ist:

```text
KI-Agenten dürfen Wissen bearbeiten – aber nachvollziehbar, berechtigt und prüfbar.
```
