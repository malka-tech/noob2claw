# Noob2Claw – Folge 11: Startprompt

# Wir bauen unseren Agentenchat

Du arbeitest am bestehenden Projekt **Noob2Claw**.

In den bisherigen Folgen wurde das System schrittweise aufgebaut:

- Folge 5: OpenClaw installieren
- Folge 6: Hermes installieren
- Folge 7: Debian-Webserver installieren
- Folge 8: Softwarearchitektur
- Folge 9: Framework und Grundsystem
- Folge 10: Agentenverwaltung, Agenten-API und Noob2Claw-Client

In **Folge 11** wird aus der reinen Agentenverwaltung ein nutzbares Chat-System.

Das Ziel dieser Aufgabe ist der Aufbau eines professionellen Agentenchats, mit dem Benutzer im Webinterface direkt mit den vorhandenen Agenten schreiben können.

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

Lies zusätzlich die Dokumentation aus Folge 10 vollständig ein.

Suche dazu im Repository nach:

```text
*Agentenverwaltung_und_Client.md
```

Beispiel:

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '*Agentenverwaltung_und_Client.md'
```

Diese Folge 10 ist technisch wichtig, weil der neue Chat nicht direkt mit OpenClaw, Hermes, Codex, Grok oder Claude spricht.

Der Chat erzeugt Aufträge im Core.

Die bestehenden Agenten-Clients holen diese Aufträge ab, führen sie aus und liefern die Antwort zurück.

---

# 3. Aufgabenbeschreibung für Folge 11 finden

Suche im gesamten Repository nach der Datei:

```text
1_Agentenchat.md
```

Verwende dazu:

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '1_Agentenchat.md'
```

Falls die Datei mit führender Null oder abweichender Nummer gespeichert wurde, suche zusätzlich:

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '*Agentenchat*.md'
```

Lies die gefundene Datei vollständig ein.

Diese Datei ist die primäre Aufgabenbeschreibung für Folge 11.

---

# 4. Bestehendes Noob2Claw-System analysieren

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
```

Suche vorhandene Dateien und Funktionen für:

- Agenten
- Agenten-Clients
- Agenten-Aufträge
- Client-API
- Benutzer
- Benutzergruppen
- Rechte
- Einstellungen
- Sessions
- API-Log
- Systemlog
- Uploads
- Navigation
- Formulare
- Tabellen
- Datenbankzugriffe
- Fehlerbehandlung

Verwende bestehende Funktionen und Strukturen.

Baue kein paralleles Chatsystem neben dem bestehenden Projekt auf.

---

# 5. Ziel von Folge 11

Setze ein vollständiges Chatmodul um.

Der Benutzer soll im Webinterface einen Agenten auswählen und mit diesem Agenten schreiben können.

Die Oberfläche soll so aufgebaut sein:

```text
┌─────────────────────────────────────────────────────────────┐
│ Noob2Claw                                                   │
├───────────────────┬─────────────────────────────────────────┤
│ Agenten links     │ Chat rechts                             │
│                   │                                         │
│ Suche             │ Header mit Agent                        │
│ Agent 1           │ Status / Modell / Client                │
│ Agent 2           │                                         │
│ Agent 3           │ Nachrichten als Sprechblasen             │
│                   │ links = Agent                           │
│ Status            │ rechts = Benutzer                       │
│ Avatar            │                                         │
│ letzter Chat      │ Eingabebereich unten                    │
│                   │ Upload / Senden / Status                │
└───────────────────┴─────────────────────────────────────────┘
```

Pflicht:

- links eine professionelle Agentenliste
- rechts der Chat mit dem ausgewählten Agenten
- Nachrichten als Sprechblasen
- Benutzer-Nachrichten rechts
- Agenten-Nachrichten links
- Systemmeldungen dezent mittig
- Chat-Verlauf dauerhaft speichern
- neue Nachricht erzeugt einen Agentenauftrag
- Agenten-Antwort wird dem Chat zugeordnet
- Upload eines Agentenbildes möglich
- Upload von Chat-Dateien möglich
- klare Statusanzeige, ob der Agent arbeitet
- klare Statusanzeige, ob eine Antwort noch aussteht
- professionelle, strukturierte Oberfläche

---

# 6. Keine Abweichung von der bestehenden Architektur

Verbindlich:

- `api.php` bleibt der einzige öffentliche API-Endpunkt.
- `mcp.php` bleibt der einzige öffentliche MCP-Endpunkt.
- Inhaltsseiten liegen unter `nav/`.
- Business-Logik liegt unter `inc/`.
- SQL liegt unter `sql/`.
- Uploads liegen unter `uploads/`.
- JavaScript liegt unter `js/`.
- CSS liegt unter `css/`.
- Keine SQL-Abfragen in `nav/`.
- Keine Business-Logik in `index.php`.
- Keine parallele Benutzerverwaltung.
- Keine parallele Rechteverwaltung.
- Keine parallele API-Struktur.
- Keine externen Frameworks.
- Kein Laravel.
- Kein Symfony.
- Kein React.
- Kein Vue.
- Kein Composer-Zwang.
- Keine Dummy-Funktionen.
- Keine unechten Beispielantworten als produktive Antworten.
- Keine erfundenen Agentenantworten.
- Keine direkte Ausführung von Shell-Befehlen aus dem Browser heraus.

---

# 7. Chatmodul vollständig umsetzen

Setze sämtliche Anforderungen aus:

```text
1_Agentenchat.md
```

vollständig um.

Dazu gehören insbesondere:

- neue Navigationsseite `nav/work_agenten_chat.php`
- Agentenliste links
- Chatbereich rechts
- Sprechblasen-Layout
- Chat-Eingabe
- Datei-Upload
- Agentenbild-Upload
- Chatverlauf
- Statusanzeige
- Agentenauftrag bei Benutzernachricht
- Zuordnung des Ergebnisses zum Chat
- Fehleranzeige im Chat
- Berechtigungen
- Datenbanktabellen
- Migrationen
- API-Aktionen
- JavaScript für Chat-Interaktion
- CSS für professionelle Chatoberfläche
- Tests
- Dokumentation

---

# 8. Datenbankregeln

Prüfe zuerst das vorhandene Datenbankschema.

Vorhandene Tabellen und Felder dürfen nicht unnötig dupliziert werden.

Für neue Tabellen gelten die bestehenden Noob2Claw-Regeln:

- kein unnötiges `NULL`
- stattdessen geeignete Standardwerte wie `0`, `''`, `{}` oder `[]`
- Primärschlüssel `ID`
- sinnvolle Indizes
- Zeitstempel als Unix-Timestamp
- keine ungültigen Zero-Dates
- keine destruktiven Migrationen
- kein `DROP TABLE`
- kein `TRUNCATE`
- Migrationen müssen mehrfach ausführbar sein
- optionale Beziehungen nutzen `0`
- Business-Logik validiert optionale Beziehungen

---

# 9. Neue Tabellen und Erweiterungen

Erstelle oder erweitere die Tabellen aus der Folge-11-Definition.

Mindestens erforderlich:

```text
agenten_chats
agenten_chat_nachrichten
agenten_chat_dateien
agenten_chat_status
```

Prüfe zusätzlich, ob die Tabelle `agents` sinnvoll erweitert werden muss um:

```text
avatar_datei_id
avatar_pfad
avatar_initialen
avatar_farbe
chat_aktiv
chat_sortierung
```

Wenn die bestehende Agententabelle aus Folge 10 andere Feldnamen nutzt, passe die Folge-11-Implementierung an die vorhandene Struktur an.

Keine bestehende Agententabelle ersetzen.

---

# 10. Rechte

Integriere das Chatmodul in die bestehende Rechteverwaltung.

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

Navigationselemente dürfen nur angezeigt werden, wenn der Benutzer das entsprechende Recht besitzt.

Serverseitige Verarbeitung muss jedes Recht erneut prüfen.

Eine reine Ausblendung im Menü reicht nicht aus.

---

# 11. Upload-Regeln

Agentenbilder:

```text
uploads/agents/avatars/
```

Chat-Dateien:

```text
uploads/agenten_chat/
```

Pflichtregeln:

- keine Originaldateinamen als Speichername
- zufällige sichere Dateinamen
- erlaubte Bildtypen für Agentenbilder: PNG, JPG, JPEG, WEBP
- optional SVG nur, wenn sicher serverseitig bereinigt oder blockiert
- Dateigröße begrenzen
- MIME-Type serverseitig prüfen
- Dateiendung nicht allein vertrauen
- SHA256 speichern
- keine PHP-Ausführung im Upload-Verzeichnis
- direkte Auslieferung sensibler Dateien verhindern
- Uploads dem Benutzer, Agenten, Chat und der Nachricht zuordnen
- Fehlermeldungen sauber ausgeben
- keine absoluten Serverpfade im Frontend anzeigen

---

# 12. Chat-Ablauf

Der fachliche Ablauf ist verbindlich:

```text
Benutzer schreibt Nachricht
        │
        ▼
Webinterface validiert CSRF und Rechte
        │
        ▼
Core speichert Benutzer-Nachricht
        │
        ▼
Core erstellt agenten_auftraege-Eintrag
        │
        ▼
Agenten-Client holt Auftrag ab
        │
        ▼
Client führt Chat über OpenClaw/Hermes/Codex/Grok/Claude aus
        │
        ▼
Client sendet Ergebnis an Core
        │
        ▼
Core speichert Agenten-Nachricht
        │
        ▼
Webinterface zeigt Antwort im Chat
```

Der Browser spricht niemals direkt mit OpenClaw, Hermes, Codex, Grok oder Claude CLI.

---

# 13. Polling statt WebSocket

Implementiere zunächst robustes AJAX-Polling.

WebSocket darf nur vorbereitet werden, wenn es ohne neue externe Abhängigkeiten und ohne parallele Architektur möglich ist.

Pflicht:

- neue Nachrichten regelmäßig nachladen
- offene Aufträge anzeigen
- Fehlerzustände anzeigen
- Polling stoppen, wenn Seite nicht sichtbar ist
- Polling nach Fehlern verlangsamen
- keine Endlosschleife ohne Pause
- keine doppelten Nachrichten anzeigen
- Nachrichten anhand stabiler IDs abgleichen

---

# 14. JavaScript

Erstelle eine eigene Datei:

```text
js/agenten_chat.js
```

Regeln:

- kein Inline-JavaScript in der Navigationsseite
- kein Token im JavaScript
- CSRF-Token nur kontrolliert über vorhandene Mechanik
- `fetch()` mit JSON
- saubere Fehlerbehandlung
- Ladezustand beim Senden
- Eingabefeld gegen Doppelsenden sperren
- Enter sendet Nachricht
- Shift+Enter erzeugt Zeilenumbruch
- automatische Scroll-Logik
- Scroll nicht erzwingen, wenn der Benutzer alte Nachrichten liest
- Dateiuploads über FormData
- Statusanzeige aktualisieren

---

# 15. CSS

Erstelle oder erweitere:

```text
css/agenten_chat.css
```

Regeln:

- zentrale Design Tokens verwenden
- Light Mode und Dark Mode unterstützen
- keine hart codierten Projektfarben außerhalb zentraler Tokens
- keine Inline-Styles
- responsive Layout
- professionelle Agentenliste
- klare aktive Auswahl
- Sprechblasen links und rechts
- Upload-Vorschau
- leere Zustände
- Ladezustände
- Fehlerzustände
- mobile Ansicht: Agentenliste und Chat sauber nutzbar

Die Optik soll modern und sehr hochwertig wirken.

Nicht billig.

Nicht nach Bastelprojekt.

---

# 16. API-Aktionen

Erweitere `api.php` nur als Router.

Business-Logik gehört in neue oder bestehende Funktionsdateien.

Mindestens neue API-Aktionen:

```text
modul=agenten_chat&aktion=agenten_liste
modul=agenten_chat&aktion=chat_oeffnen
modul=agenten_chat&aktion=nachrichten_liste
modul=agenten_chat&aktion=nachricht_senden
modul=agenten_chat&aktion=datei_hochladen
modul=agenten_chat&aktion=agentenbild_hochladen
modul=agenten_chat&aktion=status
modul=agenten_chat&aktion=chat_archivieren
```

Antwortformat:

```json
{
  "erfolg": true,
  "meldung": "",
  "daten": {},
  "request_id": ""
}
```

Fehlerformat:

```json
{
  "erfolg": false,
  "meldung": "Beschreibung des Fehlers",
  "daten": {},
  "request_id": ""
}
```

Kein `null`.

---

# 17. Funktionsdateien

Erstelle oder erweitere mindestens:

```text
inc/funktionen_agenten_chat.php
inc/funktionen_agenten_chat_nachrichten.php
inc/funktionen_agenten_chat_dateien.php
inc/funktionen_agenten_chat_status.php
inc/funktionen_uploads.php
```

Wenn vorhandene Uploadfunktionen existieren, diese verwenden oder sauber erweitern.

Regeln:

- keine SQL-Abfragen in `nav/`
- keine HTML-Ausgabe in Business-Funktionen
- keine JSON-Ausgabe in Business-Funktionen
- API-Routing und Business-Logik trennen
- Funktionen klein und sprechend benennen
- Validierung zentralisieren
- Fehlermeldungen loggen
- sensible Werte maskieren

---

# 18. Verbindung zur Agenten-Auftragsverwaltung

Beim Senden einer Chatnachricht muss ein Auftrag in der bestehenden Tabelle der Agenten-Aufträge erzeugt werden.

Der Auftrag erhält mindestens:

```text
aktion = chat
referenz = agenten_chat
referenz_id = CHAT_ID oder NACHRICHT_ID
agent_id = ausgewählter Agent
client_id = bevorzugter Client des Agenten oder 0
text = Benutzer-Nachricht
payload_json = vollständiger Chat-Kontext
dateien_json = zugeordnete Dateien
status = offen
```

Beim Rücklauf des Client-Ergebnisses muss der Core:

1. Auftrag validieren
2. zugehörigen Chat finden
3. zugehörige Benutzernachricht finden
4. Agentenantwort speichern
5. Chatstatus aktualisieren
6. Nachricht als erledigt markieren
7. Fehler sauber im Chat anzeigen, falls die Ausführung fehlgeschlagen ist

Wenn Folge 10 die Ergebnisverarbeitung bereits implementiert hat, darf diese nicht ersetzt werden.

Sie muss erweitert werden.

---

# 19. Chat-Kontext

Der Auftrag an den Agenten soll einen sinnvollen Kontext enthalten.

Mindestens:

- Systemprompt des Agenten
- aktuelle Benutzernachricht
- letzte relevante Nachrichten im Chat
- Dateihinweise
- Agenten-Metadaten
- Benutzer-ID oder Benutzername ohne sensible Daten
- gewünschtes Ausgabeformat

Keine Passwörter.

Keine API-Tokens.

Keine Sessiondaten.

Keine vollständigen internen Logs.

---

# 20. Agentenbild

Agenten sollen ein eigenes Bild bekommen können, damit der Chat persönlicher wirkt.

Pflicht:

- Upload auf Agenten-Detailseite oder im Chat
- Bild prüfen
- Bild speichern
- Agent zuordnen
- Vorschau anzeigen
- fallback auf Initialen, wenn kein Bild vorhanden ist
- Bild bei Agentenliste anzeigen
- Bild im Chatheader anzeigen
- Bild neben Agenten-Sprechblasen anzeigen
- Cache-Busting bei Änderung
- altes Bild nicht hart löschen, wenn es noch referenziert wird

---

# 21. UI-Anforderungen

Der Chat muss professionell und strukturiert aussehen.

Pflichtbereiche:

## Linke Spalte

- Suchfeld für Agenten
- Filter für aktive Agenten
- Agentenbild oder Initialen
- Agentenname
- Rolle oder Beschreibung
- Statusbadge
- letzter Chat-Auszug
- Zeitpunkt letzter Aktivität
- aktive Auswahl klar sichtbar

## Rechte Spalte

- Chatheader
- Agentenbild
- Agentenname
- Rolle
- Modell
- Clientstatus
- Nachrichtenbereich
- Sprechblasen
- Upload-Vorschau
- Eingabefeld
- Sendebutton
- Schreibstatus
- Fehleranzeige

## Nachrichten

- rechts: Benutzer
- links: Agent
- mittig: System
- Zeitstempel
- Status
- Fehlerzustand
- Anhänge
- lange Texte sauber umbrechen
- Codeblöcke lesbar darstellen
- Markdown-Grundformatierung sicher anzeigen

---

# 22. Markdown und Codeausgabe

Agenten können Code zurückgeben.

Pflicht:

- Markdown nicht ungeprüft als HTML rendern
- XSS verhindern
- Codeblöcke lesbar darstellen
- lange Zeilen umbrechen oder horizontal scrollen
- keine unkontrollierte HTML-Ausführung
- Links sicher behandeln
- Bilder aus Agentenantworten nicht blind extern einbetten

---

# 23. Sicherheit

Beachte zwingend:

- CSRF-Schutz
- Rechteprüfung
- XSS-Schutz
- sichere Uploadprüfung
- keine Token im Frontend
- keine Secrets in Logs
- keine absoluten Serverpfade im Browser
- keine unkontrollierten Shell-Befehle
- keine Direktkommunikation Browser → Agenten-Client
- keine SQL-Abfragen mit ungesicherten Eingaben
- keine öffentliche Auslieferung interner Dateien
- keine PHP-Ausführung unter Uploads
- keine doppelte Auftragserzeugung bei Doppelklick
- rate limit oder einfache Schutzlogik gegen Spam
- technische Fehler nur loggen, nicht im Browser ausgeben

---

# 24. Tests

Führe nach der Implementierung mindestens aus:

## PHP-Syntax

```bash
find /mnt/noob2claw \
-type f \
-name '*.php' \
-print0 | xargs -0 -n1 php -l
```

## JavaScript-Dateien prüfen

```bash
find /mnt/noob2claw/js \
-type f \
-name '*.js' \
-print
```

## CSS-Dateien prüfen

```bash
find /mnt/noob2claw/css \
-type f \
-name '*.css' \
-print
```

## Upload-Verzeichnisse prüfen

```bash
ls -la /mnt/noob2claw/uploads/
ls -la /mnt/noob2claw/uploads/agents/
ls -la /mnt/noob2claw/uploads/agents/avatars/
ls -la /mnt/noob2claw/uploads/agenten_chat/
```

## Webprüfung

Nutze die in Folge 9 definierte Test-URL.

Prüfe mindestens:

```text
/
 /login.php
 /api.php?modul=system&aktion=status
 /index.php?seite=work_agenten_chat
```

## Funktionstest

Teste mindestens:

1. Chatseite öffnet ohne PHP-Fehler
2. Agentenliste lädt
3. Agent ohne Bild zeigt Initialen
4. Agent mit Bild zeigt Avatar
5. Nachricht senden
6. Nachricht wird rechts angezeigt
7. Auftrag wird erzeugt
8. Status zeigt ausstehende Antwort
9. Client verarbeitet Auftrag
10. Agentenantwort erscheint links
11. Fehler im Auftrag erscheint als Fehlernachricht
12. Datei hochladen
13. unzulässige Datei wird abgelehnt
14. Agentenbild hochladen
15. Rechteprüfung ohne Berechtigung
16. CSRF-Schutz
17. XSS-Test mit `<script>`
18. Mobile Ansicht

Behaupte keine erfolgreichen Tests, wenn sie nicht durchgeführt wurden.

---

# 25. Dokumentation erstellen

Erstelle oder aktualisiere:

```text
/mnt/noob2claw/README.md
```

Erstelle zusätzlich:

```text
/mnt/noob2claw/docs/folge_11_agentenchat/README.md
```

Die Dokumentation muss enthalten:

- Ziel
- Architektur
- Navigationsseite
- Datenbanktabellen
- API-Aktionen
- Uploadpfade
- Chatablauf
- Verbindung zur Agenten-Auftragsverwaltung
- Rechte
- Sicherheit
- Tests
- bekannte Grenzen

---

# 26. Abschlussbericht

Beende die Aufgabe erst, wenn die vollständige Umsetzung erfolgt und geprüft wurde.

Gib anschließend einen strukturierten Abschlussbericht aus mit:

1. umgesetzten Funktionen
2. neu erstellten Dateien
3. geänderten Dateien
4. neuen Datenbanktabellen
5. geänderten Datenbanktabellen
6. neuen API-Aktionen
7. neuen Rechte
8. Uploadpfade
9. Sicherheitsmaßnahmen
10. durchgeführten Tests
11. Testergebnisse
12. gefundenen Problemen
13. behobenen Problemen
14. noch offenen Punkten

Behaupte keine erfolgreiche Umsetzung, wenn Tests fehlgeschlagen sind.

Bei einem Fehler:

- Fehlerursache ermitteln
- Fehler beheben
- Test erneut ausführen
- Ergebnis dokumentieren

Beginne jetzt mit dem Aktualisieren des GitHub-Repositories und arbeite die Aufgabe danach vollständig ab.
