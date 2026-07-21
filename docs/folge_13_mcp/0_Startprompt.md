# Noob2Claw – Folge 13: Startprompt

# Wir bauen MCP-Funktionen für das Wiki

Du arbeitest am bestehenden Projekt **Noob2Claw**.

In den bisherigen Folgen wurde das System schrittweise aufgebaut:

- Folge 5: OpenClaw installieren
- Folge 6: Hermes installieren
- Folge 7: Debian-Webserver installieren
- Folge 8: Softwarearchitektur
- Folge 9: Framework und Grundsystem
- Folge 10: Agentenverwaltung, Agenten-API und Noob2Claw-Client
- Folge 11: Agentenchat
- Folge 12: Wiki per Vibe Coding

In **Folge 13** wird das bestehende Wiki für KI-Agenten über MCP nutzbar gemacht.

Das Ziel ist nicht, ein neues Wiki zu bauen.

Das Ziel ist:

```text
Bestehendes Wiki
    +
MCP-Server-Funktionen
    +
MCP-Zugänge und Rechte
    +
Agenten-Zuordnung per Token-Suffix
    +
saubere Versionen mit Agenten-Nachweis
```

Am Ende soll ein OpenClaw-Agent über den bestehenden Noob2Claw-MCP-Server auf das Wiki zugreifen können.

---

# 1. GitHub-Repository aktualisieren

Das Repository befindet sich unter:

```text
https://github.com/malka-tech/noob2claw.git
```

Der lokale Zielpfad lautet:

```text
/home/noobclaw/noob2claw_vorlage/
```

Prüfe zuerst, ob das Repository bereits vorhanden ist.

Wenn es noch nicht vorhanden ist, führe aus:

```bash
git clone https://github.com/malka-tech/noob2claw.git \
/home/noobclaw/noob2claw_vorlage/
```

Wenn das Repository bereits vorhanden ist, aktualisiere es:

```bash
git -C /home/noobclaw/noob2claw_vorlage/ fetch origin
git -C /home/noobclaw/noob2claw_vorlage/ reset --hard origin/main
```

Prüfe anschließend den aktuellen Git-Stand:

```bash
git -C /home/noobclaw/noob2claw_vorlage/ status
git -C /home/noobclaw/noob2claw_vorlage/ log -1 --oneline
```

---

# 2. Verbindliche Entwicklungsgrundlagen einlesen

Lies vor Beginn der Entwicklung sämtliche Markdown-Dateien aus:

```text
/home/noobclaw/noob2claw_vorlage/docs/folge_9_framework/
```

Diese Dokumente sind verbindlich für:

- Softwarearchitektur
- Verzeichnisstruktur
- Datenbankstruktur
- PHP-Coding-Rules
- Sicherheit
- Benutzer- und Rechteverwaltung
- Funktionen und Module
- Navigation
- Formulare
- Tabellen
- CSS
- Design
- REST-API
- MCP
- Einstellungen
- Fehlerbehandlung
- Logging

Besonders wichtig sind:

```text
07_API_und_MCP.md
08_Benutzer_und_Rechte.md
09_Module_und_Funktionen.md
11_Datenbanktabellen_Core.md
13_Design_und_UI_Standards.md
15_Formular_Standards.md
16_Tabellen_Standards.md
```

Die wichtigsten Regeln daraus:

- `mcp.php` bleibt der einzige öffentliche MCP-Endpunkt.
- `api.php` bleibt der einzige öffentliche REST-Endpunkt.
- Business-Logik liegt ausschließlich unter `inc/`.
- Web, API und MCP verwenden dieselben zentralen Funktionen.
- Keine SQL-Abfragen in `mcp.php`.
- Keine SQL-Abfragen in Navigationsdateien.
- Keine doppelte Wiki-Logik nur für MCP.
- Rechte werden zentral geprüft.
- Zugangsschlüssel werden niemals im Klartext gespeichert.
- Tokens, Secrets und Authorization-Header werden niemals unmaskiert geloggt.

---

# 3. Vorherige Folgen einlesen

Suche und lies zusätzlich die Dokumentation der vorherigen Folge-Dateien ein.

## Folge 10

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '*Agentenverwaltung_und_Client.md'
```

Wichtig für:

- Agenten
- Agenten-Clients
- Auftragsverwaltung
- Status und Logs
- Agenten-Zuordnung

## Folge 11

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '*Agentenchat*.md'
```

Wichtig für:

- Agentenkommunikation
- Benutzer- und Agenten-Nachrichten
- Agentenbilder
- Chat-Aufträge

## Folge 12

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '*wiki*.md' -o -name '*Wiki*.md'
```

Wichtig für:

- Wiki-Kategorien
- Wiki-Artikel
- Wiki-Versionen
- Wiki-Dateiuploads
- Wiki-Rechte
- Wiki-Suche

Falls die Wiki-Implementierung im aktiven Projekt nicht existiert, wird **kein neues Parallel-Wiki** erfunden.

Dann gilt:

1. Prüfe, ob Folge 12 unvollständig umgesetzt wurde.
2. Dokumentiere den fehlenden Wiki-Bestand sauber.
3. Setze MCP-Funktionen nur auf vorhandene oder sauber nachgezogene Wiki-Business-Funktionen auf.
4. Baue keine isolierte MCP-Wiki-Datenhaltung neben dem eigentlichen Wiki.

---

# 4. Aufgabenbeschreibung für Folge 13 finden

Suche im gesamten Repository nach der Datei:

```text
1_MCP_Defintion.md
```

Verwende dazu:

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '1_MCP_Defintion.md'
```

Falls die Datei anders benannt wurde, suche zusätzlich:

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '*MCP*Wiki*.md' -o -name '*MCP*.md'
```

Lies die gefundene Datei vollständig ein.

Diese Datei ist die primäre Aufgabenbeschreibung für Folge 13.

---

# 5. Aktives Projekt analysieren

Das aktive Webprojekt ist über SSHFS eingebunden und befindet sich unter:

```text
/mnt/noob2claw/
```

Untersuche vor Änderungen vollständig den bestehenden Projektstand.

Prüfe insbesondere:

```text
/mnt/noob2claw/index.php
/mnt/noob2claw/api.php
/mnt/noob2claw/mcp.php
/mnt/noob2claw/inc/
/mnt/noob2claw/nav/
/mnt/noob2claw/css/
/mnt/noob2claw/js/
/mnt/noob2claw/uploads/
/mnt/noob2claw/sql/
```

Suche vorhandene Dateien und Funktionen für:

- Wiki
- Wiki-Kategorien
- Wiki-Artikel
- Wiki-Versionen
- Wiki-Dateien
- Wiki-Suche
- Wiki-Rechte
- MCP-Benutzer
- MCP-Zugänge
- MCP-Logs
- MCP-Routing
- MCP-Tools
- MCP-Resources
- Agenten
- Benutzer
- Rechte
- Navigation
- Einstellungen
- Uploads
- Dateiauslieferung
- Fehlerbehandlung
- Logging

Verwende bestehende Funktionen und Strukturen.

Baue kein zweites Wiki.

Baue keinen zweiten MCP-Server.

---

# 6. Ziel von Folge 13

Setze die MCP-Erweiterung für das Wiki vollständig um.

Pflichtziele:

1. Prüfen und Fertigstellen aller MCP-Masken im Webinterface
2. Verwaltung von MCP-Zugängen, Tools, Resources, Rechten und erlaubten Agenten
3. Bereitstellung einer MCP-Funktionsdatei für das Wiki
4. Erweiterung der MCP-Authentifizierung um Agenten-Zuordnung per Token-Suffix
5. Anzeige von Agenten-Änderungen in den Wiki-Versionen
6. Erstellung eines MCP-Zugangs in der Maske
7. Übergabe der Zugangsdaten an den OpenClaw-Agenten
8. OpenClaw-Agent richtet den MCP-Server selbst in `~/.openclaw/openclaw.json` ein
9. Testaufruf vom OpenClaw-Agenten an das Wiki

---

# 7. MCP-Masken prüfen und fertigstellen

Prüfe zuerst alle vorhandenen MCP-Masken.

Mindestens relevant:

```text
nav/settings_mcp_benutzer.php
nav/control_mcp_log.php
```

Falls vorhanden, zusätzlich prüfen:

```text
nav/settings_mcp_rechte.php
nav/settings_mcp_tools.php
nav/control_mcp_tools.php
nav/control_mcp_resources.php
```

Falls notwendige Masken fehlen, erstelle sie gemäß bestehender Navigation, Formular- und Tabellenstandards.

Die MCP-Verwaltung muss mindestens können:

- MCP-Zugang anlegen
- MCP-Zugang bearbeiten
- MCP-Zugang deaktivieren
- MCP-Zugang sperren
- MCP-Schlüssel erzeugen
- MCP-Schlüssel neu erzeugen
- Schlüssel nur einmal im Klartext anzeigen
- keine Schlüssel im Klartext speichern
- Tools erlauben oder sperren
- Resources erlauben oder sperren
- Rechte zuordnen
- IP-Beschränkung pflegen
- Gültigkeit ab/bis pflegen
- erlaubte Agenten zuordnen
- Agenten-Schreibrechte festlegen
- Agenten-Leserechte festlegen
- letzten Zugriff anzeigen
- letzte IP anzeigen
- MCP-Logs anzeigen
- Fehler anzeigen
- Test- und OpenClaw-Konfigurationshinweis anzeigen

Die Maske muss klar sichtbar machen:

```text
MCP-Zugang ≠ Agent
```

Der MCP-Zugang authentifiziert den technischen Zugriff.

Die Agent-ID im Token-Suffix dokumentiert und begrenzt, welcher Agent handelt.

---

# 8. MCP-Authentifizierung mit Agent-ID erweitern

Der bestehende MCP-Zugang wird weiterhin über Bearer Token authentifiziert.

Neu ist ein optionaler Agenten-Suffix hinter einem Doppelpunkt:

```http
Authorization: Bearer MCP_TOKEN:AGENT_ID
```

Beispiel:

```http
Authorization: Bearer n2c_mcp_abcdef123456:17
```

Regeln:

- Der Teil vor dem letzten Doppelpunkt ist der echte MCP-Token.
- Der Teil nach dem letzten Doppelpunkt ist die Agent-ID.
- Die Agent-ID muss eine positive Ganzzahl sein.
- Die Agent-ID darf niemals den Token ersetzen.
- Die Agent-ID alleine authentifiziert nichts.
- Ohne gültigen MCP-Token gibt es keinen Zugriff.
- Ohne erlaubte Agenten-Zuordnung gibt es keinen Schreibzugriff.
- Schreibende Wiki-Tools benötigen eine gültige Agent-ID.
- Lesende Wiki-Tools dürfen optional ohne Agent-ID erlaubt werden, wenn der MCP-Zugang dafür berechtigt ist.
- Bestehende MCP-Zugänge ohne Agent-Suffix bleiben für bestehende read-only Anwendungsfälle kompatibel.

Wichtig:

```text
Token = Authentifizierung
Agent-ID = Zuordnung / Audit / Berechtigungsbegrenzung
```

Implementiere die Verarbeitung zentral in der MCP-Authentifizierung.

Nicht jede Wiki-Funktion darf selbst Token zerlegen.

---

# 9. Erwarteter Auth-Kontext

Nach erfolgreicher Authentifizierung muss ein zentraler MCP-Kontext verfügbar sein.

Beispiel:

```php
$mcp_kontext = [
    'mcp_benutzer_id' => 5,
    'agent_id' => 17,
    'agent_bezeichnung' => 'Claudia Wiki',
    'quelle' => 'mcp',
    'request_id' => $request_id,
    'client_name' => $client_name,
    'client_version' => $client_version,
    'ip_adresse' => $ip_adresse,
];
```

Wenn keine Agent-ID übergeben wurde:

```php
'agent_id' => 0
'agent_bezeichnung' => ''
```

Für schreibende Wiki-Tools ist `agent_id = 0` nicht ausreichend.

---

# 10. MCP-Wiki-Funktionsdatei bereitstellen

Erstelle oder vervollständige:

```text
/mnt/noob2claw/inc/funktionen_mcp_wiki.php
```

Diese Datei ist **nicht** die Wiki-Business-Logik.

Sie darf:

- Wiki-MCP-Tools definieren
- Tool-Namen dokumentieren
- Parameter-Schemas bereitstellen
- Tool-Aufrufe auf zentrale Wiki-Funktionen routen
- Berechtigungen für MCP-Wiki-Tools prüfen
- einheitliche Ergebnisarrays zurückgeben

Sie darf nicht:

- eigene Wiki-Tabellen direkt per SQL bearbeiten
- HTML erzeugen
- fertige MCP-Protokollantworten erzeugen
- doppelte Wiki-Funktionen implementieren
- Tokens verarbeiten
- eigene Authentifizierung bauen

Die eigentliche Wiki-Logik bleibt in den vorhandenen Wiki-Funktionsdateien aus Folge 12.

Falls diese fehlen, sind sie dort fachlich korrekt zu ergänzen, nicht in `funktionen_mcp_wiki.php`.

---

# 11. Pflicht-Tools für das Wiki

Der MCP-Server soll mindestens folgende Tools bereitstellen:

```text
wiki_artikel_suchen
wiki_artikel_laden
wiki_artikel_anlegen
wiki_artikel_bearbeiten
wiki_artikel_veroeffentlichen
wiki_artikel_archivieren
wiki_kategorien_liste
wiki_kategorie_laden
wiki_kategorie_anlegen
wiki_kategorie_bearbeiten
wiki_dateien_liste
wiki_datei_hochladen
wiki_datei_laden
wiki_versionen_liste
wiki_version_laden
```

Optional, wenn im Wiki vorhanden:

```text
wiki_artikel_loeschen
wiki_artikel_wiederherstellen
wiki_tags_liste
wiki_tags_setzen
wiki_artikel_verlinken
wiki_markdown_vorschau
```

Jedes Tool muss:

- eine klare Beschreibung besitzen
- Parameter validieren
- Rechte prüfen
- Agenten-Zuordnung prüfen
- keine unnötigen Daten zurückgeben
- sensible Daten aus Antworten entfernen
- Fehler sauber zurückgeben
- einen Logeintrag erzeugen

---

# 12. Wiki-Versionen um Agenten-Nachweis erweitern

Prüfe die bestehende Wiki-Versionierung.

Die Versionen müssen nachvollziehbar zeigen:

- wer geändert hat
- ob es ein Web-Benutzer war
- ob es ein MCP-Zugriff war
- welcher MCP-Zugang verwendet wurde
- welcher Agent übergeben wurde
- wann die Änderung erfolgte
- welcher Artikel betroffen war
- welche Aktion ausgeführt wurde
- optional: Änderungsnotiz

Falls notwendige Felder fehlen, erstelle eine idempotente Migration.

Geeignete Felder sind zum Beispiel:

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

Regeln:

- Keine `NULL`-Werte.
- Nicht gesetzte IDs sind `0`.
- Nicht gesetzte Texte sind `''`.
- Der Agentenname soll zusätzlich als Snapshot gespeichert werden, damit Versionen auch nach Umbenennung oder Deaktivierung nachvollziehbar bleiben.
- Tokens werden niemals gespeichert.
- Vollständige Authorization-Header werden niemals gespeichert.

In der Wiki-Versionen-Ansicht muss sichtbar sein:

```text
Geändert durch Agent: Claudia Wiki
über MCP-Zugang: OpenClaw Wiki Access
```

Fallbacks:

```text
Geändert durch Benutzer: Mario
Geändert durch MCP-Zugang: OpenClaw Wiki Access
Geändert durch System
```

---

# 13. Datenbankänderungen

Prüfe zuerst das vorhandene Datenbankschema.

Vorhandene Tabellen und Felder dürfen nicht unnötig dupliziert werden.

Neue oder erweiterte Strukturen müssen mehrfach ausführbar sein.

Kein `DROP TABLE`.

Kein `TRUNCATE`.

Keine destruktive Migration.

Empfohlene Ergänzungen, falls noch nicht vorhanden:

## Tabelle `mcp_benutzer_agents`

Ordnet MCP-Zugänge erlaubten Agenten zu.

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

Eindeutigkeit:

```text
mcp_benutzer_id + agent_id
```

## Erweiterung `mcp_log`

Falls noch nicht vorhanden:

```text
agent_id
```

## Erweiterung Wiki-Versionen

Die tatsächliche Tabelle hängt von der Folge-12-Implementierung ab.

Ergänze dort, wo Wiki-Versionen gespeichert werden, mindestens:

```text
quelle
mcp_benutzer_id
agent_id
akteur_typ
akteur_name
request_id
```

Nutze vorhandene Tabellen, wenn diese Felder bereits anders heißen.

Dokumentiere jede Namensentscheidung im Abschlussbericht.

---

# 14. Rechte

Ergänze fehlende Rechte kontrolliert.

Mindestens sinnvoll:

```text
mcp_benutzer_anzeigen
mcp_benutzer_verwalten
mcp_benutzer_token_erzeugen
mcp_benutzer_agents_verwalten
mcp_tools_verwalten
mcp_resources_verwalten
mcp_log_anzeigen
mcp_testen
wiki_mcp_lesen
wiki_mcp_schreiben
wiki_mcp_kategorien_verwalten
wiki_mcp_dateien_hochladen
wiki_mcp_versionen_anzeigen
```

Vorhandene Rechte nicht doppelt anlegen.

Wenn bereits ähnliche Rechte existieren, verwende die vorhandenen Rechte und dokumentiere die Zuordnung.

Administratoren erhalten alle neuen Rechte.

Normale Benutzer erhalten keine neuen Rechte automatisch.

---

# 15. OpenClaw-Zugangsdaten erzeugen

Nach der Implementierung soll über die Weboberfläche ein MCP-Zugang erzeugt werden.

Beispiel:

```text
Bezeichnung: OpenClaw Wiki Access
Tools: wiki_*
Resources: wiki_*
Erlaubte Agenten: gewünschter OpenClaw-Agent
Lesen erlaubt: ja
Schreiben erlaubt: ja
IP-Beschränkung: optional
Aktiv: ja
```

Die Maske zeigt anschließend einmalig:

```text
MCP-URL
MCP-Token
Agent-ID
Authorization-Header
OpenClaw-Konfigurationsbeispiel
Testbefehle
```

Der Token darf danach nicht erneut im Klartext angezeigt werden.

---

# 16. OpenClaw-Agent richtet `openclaw.json` ein

Der OpenClaw-Agent soll die Zugangsdaten selbst in:

```text
~/.openclaw/openclaw.json
```

eintragen.

Der Agent soll vorher eine Sicherung erstellen:

```bash
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.bak.$(date +%Y%m%d_%H%M%S)
```

Der erwartete MCP-Server-Eintrag ist sinngemäß:

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

Wenn die lokale OpenClaw-Version eine andere Schreibweise benötigt, muss der Agent die installierte Version prüfen und die Konfiguration passend zur vorhandenen OpenClaw-Dokumentation umsetzen.

Danach testen:

```bash
openclaw mcp status --verbose
openclaw mcp doctor noob2claw_wiki --probe
openclaw mcp probe noob2claw_wiki --json
```

Falls `doctor --probe` nicht verfügbar ist, nutze die für die installierte Version passende Prüfvariante.

---

# 17. Testaufruf

Führe nach der Einrichtung einen echten Test durch.

Mindestens:

1. MCP-Verbindung prüfen
2. Tools auflisten
3. Wiki-Kategorien laden
4. Wiki-Artikel suchen
5. Testartikel als Agent anlegen oder bearbeiten
6. Wiki-Version prüfen
7. In der Version muss der Agent sichtbar sein
8. MCP-Log prüfen
9. Rechteverstoß testen
10. Ungültige Agent-ID testen
11. Token ohne Agent-ID bei Schreibtool testen
12. Deaktivierten Agenten testen

Beispiel-Testinhalt:

```text
Titel: MCP Test durch OpenClaw
Kategorie: Noob2Claw
Inhalt: Dieser Artikel wurde über den Noob2Claw-MCP-Server durch einen OpenClaw-Agenten erstellt.
Änderungsgrund: Testaufruf Folge 13
```

Erwartung:

```text
Wiki-Version zeigt:
Quelle: MCP
Agent: <Agentenname>
MCP-Zugang: OpenClaw Wiki Access
```

---

# 18. Sicherheit

Zwingende Regeln:

- Keine Tokens in URLs.
- Tokens nur über Authorization-Header.
- Keine Tokens im Klartext in der Datenbank.
- Keine Tokens im Klartext in Logs.
- Token-Suffix wird zentral verarbeitet.
- Agent-ID ist keine Authentifizierung.
- Agent-ID muss gegen Datenbank geprüft werden.
- MCP-Zugang muss zur Agent-ID berechtigt sein.
- Schreibtools benötigen gültigen Agenten-Kontext.
- Uploads über MCP müssen genauso streng geprüft werden wie Web-Uploads.
- Keine hochgeladenen Dateien direkt ausführbar machen.
- Keine SQL-Abfragen in `mcp.php`.
- Keine doppelte Wiki-Logik in MCP-Dateien.
- Keine HTML-Ausgabe aus Funktionsdateien.
- Keine unmaskierten Authorization-Header im Log.
- Fehler technisch loggen, aber sauber und verständlich ausgeben.

---

# 19. Prüfung und Tests

Führe mindestens aus:

## PHP-Syntax

```bash
find /mnt/noob2claw \
-type f \
-name '*.php' \
-print0 | xargs -0 -n1 php -l
```

## Migrationsprüfung

```bash
find /mnt/noob2claw/sql \
-type f \
-name '*.sql' \
-print
```

Prüfe, ob Migrationen mehrfach ausführbar sind.

## MCP-HTTP-Test

```bash
curl -sS \
-H 'Authorization: Bearer MCP_TOKEN:AGENT_ID' \
-H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' \
https://DEINE-DOMAIN/mcp.php
```

## OpenClaw-Test

```bash
openclaw mcp status --verbose
openclaw mcp doctor noob2claw_wiki --probe
openclaw mcp probe noob2claw_wiki --json
```

## Funktionstest

Teste mindestens:

1. MCP-Zugang anlegen
2. MCP-Zugang bearbeiten
3. Token neu erzeugen
4. Tools begrenzen
5. Agent zuordnen
6. Agent-Schreibrecht entfernen
7. Tool `wiki_artikel_suchen`
8. Tool `wiki_artikel_laden`
9. Tool `wiki_artikel_anlegen`
10. Tool `wiki_artikel_bearbeiten`
11. Tool `wiki_datei_hochladen`
12. Tool `wiki_versionen_liste`
13. Version zeigt Agentenname
14. MCP-Log zeigt Agent-ID
15. ungültiger Token wird abgelehnt
16. gültiger Token mit falscher Agent-ID wird abgelehnt
17. gültiger Token ohne Agent-ID darf nicht schreiben
18. deaktivierter MCP-Zugang wird abgelehnt
19. deaktivierter Agent darf nicht schreiben
20. Upload mit verbotener Dateiendung wird abgelehnt

---

# 20. Dokumentation aktualisieren

Aktualisiere mindestens:

```text
/mnt/noob2claw/README.md
```

Ergänze eine kurze technische Dokumentation für:

- MCP-Zugänge
- Agent-ID im Token-Suffix
- Wiki-MCP-Tools
- Wiki-Versionierung mit Agenten-Nachweis
- OpenClaw-Konfiguration
- Testaufrufe
- Sicherheitsregeln
- Fehlersuche

Optional zusätzlich:

```text
/mnt/noob2claw/docs/folge_13_mcp_wiki.md
```

---

# 21. Abschlussbericht

Beende die Aufgabe erst, wenn die Umsetzung geprüft wurde.

Gib anschließend einen strukturierten Abschlussbericht aus mit:

1. geprüfte MCP-Masken
2. neu erstellte Dateien
3. geänderte Dateien
4. neue Datenbanktabellen
5. geänderte Datenbanktabellen
6. neue Rechte
7. neue MCP-Tools
8. neue Resources
9. Auth-Erweiterung mit Agent-ID
10. Wiki-Versionierungsänderungen
11. OpenClaw-Konfiguration
12. Testaufrufe
13. durchgeführte Tests
14. gefundene Probleme
15. behobene Probleme
16. offene Punkte

Behaupte keine erfolgreiche Umsetzung, wenn Tests fehlgeschlagen sind.

Bei einem Fehler:

- Fehlerursache ermitteln
- Fehler beheben
- Test erneut ausführen
- Ergebnis dokumentieren

Beginne jetzt mit dem Aktualisieren des GitHub-Repositories und arbeite die Aufgabe danach vollständig ab.
