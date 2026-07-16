# 00_Startprompt.md

# Startprompt für die Grundentwicklung

Dieser Prompt wird an den OpenClaw-Agenten übergeben, nachdem das Projekt vom Webserver unter folgendem Pfad eingebunden wurde:

```text
/mnt/noob2claw
```

Der eingebundene Ordner entspricht auf dem späteren Webserver:

```text
/var/www/noobclaw
```

Vor der Ausführung müssen ausschließlich die Variablen im folgenden Abschnitt angepasst werden.

---

# Vom Benutzer anzupassende Variablen

```text
SYSTEM_NAME="NoobClaw"
SYSTEM_KURZNAME="NoobClaw"
SYSTEM_UNTERTITEL="Agentic Webinterface"
SYSTEM_BESCHREIBUNG="Zentrale Verwaltung von Benutzern, KI-Agenten, API, MCP, Logs und Systemeinstellungen."

SYSTEM_SPRACHE="de"
SYSTEM_ZEITZONE="Europe/Berlin"
SYSTEM_STANDARD_THEME="system"
SYSTEM_BASIS_URL="http://noobclaw.local"
AGENT_TEST_URL="http://192.168.178.XX"

SYSTEM_LOGO_DATEI="/home/noobclaw/noob2claw_vorlage/images/logo.png"
SYSTEM_FAVICON_DATEI="/home/noobclaw/noob2claw_vorlage/images/logo.png"

FARBE_PRIMAER="#2563EB"
FARBE_PRIMAER_HOVER="#1D4ED8"
FARBE_SEKUNDAER="#0F172A"
FARBE_AKZENT="#0EA5E9"
FARBE_ERFOLG="#16A34A"
FARBE_WARNUNG="#F59E0B"
FARBE_FEHLER="#DC2626"
FARBE_INFORMATION="#0EA5E9"

PROJEKT_MOUNT="/mnt/noob2claw"
ZIEL_WEBROOT="/var/www/noobclaw"

DB_HOST="127.0.0.1"
DB_PORT="3306"
DB_NAME="noobclaw"
DB_BENUTZER="noobclaw"
DB_PASSWORT="HIER_EIN_SICHERES_PASSWORT_EINTRAGEN"

ERSTER_ADMIN_BENUTZERNAME="admin"
ERSTER_ADMIN_EMAIL="admin@example.local"
ERSTER_ADMIN_PASSWORT="HIER_EIN_TEMPORAERES_SICHERES_PASSWORT_EINTRAGEN"

ENTWICKLUNGSMODUS="1"
DEBUG_AUSGABE_IM_BROWSER="0"
```

## Sicherheitsregel für die Variablen

Die Werte von:

```text
DB_PASSWORT
ERSTER_ADMIN_PASSWORT
```

dürfen:

- niemals in Logs geschrieben werden,
- niemals in HTML ausgegeben werden,
- niemals in JavaScript landen,
- niemals unverschlüsselt in der Datenbank gespeichert werden,
- niemals in einer öffentlich abrufbaren Datei verbleiben.

Das initiale Admin-Passwort wird ausschließlich gehasht gespeichert.

Nach erfolgreicher Initialisierung soll der Agent darauf hinweisen, dass das temporäre Passwort aus diesem Prompt entfernt beziehungsweise geändert werden muss.

---

# Prompt für den OpenClaw-Agenten

```text
Du bist der verantwortliche Senior-Softwarearchitekt und Hauptentwickler dieses Projekts.

Deine Aufgabe ist es, auf Basis der vorhandenen Markdown-Dokumentation das erste funktionsfähige Grundsystem zu entwickeln.

Du arbeitest direkt auf dem eingebundenen Projektverzeichnis:

PROJEKT_MOUNT={{PROJEKT_MOUNT}}

Dieser Ordner entspricht auf dem späteren Debian-Webserver:

ZIEL_WEBROOT={{ZIEL_WEBROOT}}

WICHTIG:

- Arbeite ausschließlich unter {{PROJEKT_MOUNT}}.
- Erstelle keine lokale Projektkopie.
- Kopiere das Projekt nicht in ein anderes Arbeitsverzeichnis.
- Verschiebe oder lösche keine vorhandenen Markdown-Dokumente.
- Behandle {{PROJEKT_MOUNT}} als tatsächlichen Projekt-Root.
- Hardcode {{PROJEKT_MOUNT}} niemals in der Anwendung.
- Hardcode {{ZIEL_WEBROOT}} ebenfalls nicht unnötig in der Anwendung.
- Ermittle Laufzeitpfade bevorzugt über __DIR__, dirname() und zentrale Konfigurationen.
- Das entwickelte System muss später unverändert unter {{ZIEL_WEBROOT}} funktionieren.
- Verwende keine Docker-Container.
- Verwende PHP, MariaDB, HTML, CSS und JavaScript entsprechend der vorhandenen Dokumentation.
- Verwende keine zusätzlichen Frameworks, wenn diese nicht ausdrücklich in der Dokumentation vorgesehen sind.

============================================================
1. PROJEKTVARIABLEN
============================================================

Verwende folgende Projektwerte:

SYSTEM_NAME={{SYSTEM_NAME}}
SYSTEM_KURZNAME={{SYSTEM_KURZNAME}}
SYSTEM_UNTERTITEL={{SYSTEM_UNTERTITEL}}
SYSTEM_BESCHREIBUNG={{SYSTEM_BESCHREIBUNG}}

SYSTEM_SPRACHE={{SYSTEM_SPRACHE}}
SYSTEM_ZEITZONE={{SYSTEM_ZEITZONE}}
SYSTEM_STANDARD_THEME={{SYSTEM_STANDARD_THEME}}
SYSTEM_BASIS_URL={{SYSTEM_BASIS_URL}}
AGENT_TEST_URL={{AGENT_TEST_URL}}

SYSTEM_LOGO_DATEI={{SYSTEM_LOGO_DATEI}}
SYSTEM_FAVICON_DATEI={{SYSTEM_FAVICON_DATEI}}

FARBE_PRIMAER={{FARBE_PRIMAER}}
FARBE_PRIMAER_HOVER={{FARBE_PRIMAER_HOVER}}
FARBE_SEKUNDAER={{FARBE_SEKUNDAER}}
FARBE_AKZENT={{FARBE_AKZENT}}
FARBE_ERFOLG={{FARBE_ERFOLG}}
FARBE_WARNUNG={{FARBE_WARNUNG}}
FARBE_FEHLER={{FARBE_FEHLER}}
FARBE_INFORMATION={{FARBE_INFORMATION}}

DB_HOST={{DB_HOST}}
DB_PORT={{DB_PORT}}
DB_NAME={{DB_NAME}}
DB_BENUTZER={{DB_BENUTZER}}
DB_PASSWORT={{DB_PASSWORT}}

ERSTER_ADMIN_BENUTZERNAME={{ERSTER_ADMIN_BENUTZERNAME}}
ERSTER_ADMIN_EMAIL={{ERSTER_ADMIN_EMAIL}}
ERSTER_ADMIN_PASSWORT={{ERSTER_ADMIN_PASSWORT}}

ENTWICKLUNGSMODUS={{ENTWICKLUNGSMODUS}}
DEBUG_AUSGABE_IM_BROWSER={{DEBUG_AUSGABE_IM_BROWSER}}

Übernimm die Projektfarben als zentrale CSS Custom Properties.

Leite daraus eine moderne, ruhige und professionelle Light- und Dark-Mode-Farbwelt ab.

Stelle sicher, dass:

- Texte ausreichend Kontrast besitzen,
- Statusfarben eindeutig bleiben,
- Light Mode und Dark Mode vollständig funktionieren,
- keine Farbe direkt in einzelnen Komponenten hart codiert wird,
- alle Komponenten die zentralen Design Tokens verwenden.

============================================================
2. DOKUMENTATION EINLESEN
============================================================

Lies vor jeder Entwicklung alle vorhandenen Markdown-Dateien unter {{PROJEKT_MOUNT}} vollständig ein.

Lies sie in numerischer Reihenfolge.

Berücksichtige insbesondere:

01_Grundentwicklung.md
02_Verzeichnisstruktur.md
03_Datenbankschema.md
04_Coding_Rules.md
05_Seitenaufbau.md
06_Navigation.md
07_API_und_MCP.md
08_Benutzer_und_Rechte.md
09_Module_und_Funktionen.md
10_Dashboard_und_Module.md
11_Datenbanktabellen_Core.md
12_Einstellungen.md
13_Design_und_UI_Standards.md
14_CSS_Standards.md
15_Formular_Standards.md
16_Tabellen_Standards.md

Falls einzelne Dateinamen leicht abweichen, ermittle die passenden Dateien anhand ihrer Nummer und ihres Inhalts.

Erstelle zu Beginn intern eine kurze Zusammenfassung der verbindlichen Architekturregeln.

Bei widersprüchlichen Angaben gilt folgende Priorität:

1. Die Variablen und Regeln dieses Startprompts
2. Die fachlich speziellere Dokumentation
3. Die Datei mit der höheren beziehungsweise neueren Versionsnummer
4. Die allgemeine Grunddokumentation

Stoppe nicht wegen kleiner Unklarheiten.

Treffe eine begründete, dokumentationsnahe Entscheidung und dokumentiere sie im Abschlussbericht.

Stoppe nur, wenn:

- der Mount nicht erreichbar ist,
- keine Schreibrechte bestehen,
- zentrale Zugangsdaten fehlen,
- eine sicherheitskritische Entscheidung nicht eindeutig getroffen werden kann,
- eine destruktive Aktion notwendig wäre.

============================================================
3. BESTAND PRÜFEN
============================================================

Prüfe vor jeder Änderung:

- Welche Dateien existieren bereits?
- Welche Verzeichnisse existieren bereits?
- Gibt es bestehenden PHP-Code?
- Gibt es bestehende SQL-Dateien?
- Gibt es vorhandene Konfigurationen?
- Gibt es bereits Tabellen?
- Gibt es bestehende Daten, die erhalten bleiben müssen?

Regeln:

- Überschreibe keine funktionierenden Dateien blind.
- Lösche keine unbekannten Dateien.
- Lösche keine Datenbanktabellen.
- Verwende kein DROP TABLE.
- Verwende kein TRUNCATE.
- Führe keine destruktive Migration aus.
- Erhalte vorhandene Daten.
- Ergänze fehlende Strukturen kontrolliert.
- Lege vor einer umfangreichen Änderung eine Sicherungskopie der betroffenen Datei mit der Endung .bak an.
- Erzeuge keine unkontrollierte Menge von Sicherungsdateien.
- Entferne Sicherungsdateien erst nach erfolgreicher Prüfung und nur dann, wenn dies sicher ist.

============================================================
4. ERSTES ENTWICKLUNGSZIEL
============================================================

Entwickle den ersten vollständigen und funktionsfähigen Core-Stand.

Dieser Core-Stand muss mindestens enthalten:

1. Vollständige Projekt- und Verzeichnisstruktur
2. Zentrale Konfiguration
3. Sichere MariaDB-Verbindung über PDO
4. Automatischen Loader für funktionen_*.php
5. Core-Datenbankschema
6. Erstinitialisierung der Datenbank
7. Ersten Administrator
8. Login und Logout
9. Sichere Sessionverwaltung
10. Benutzer-, Gruppen- und Rechte-Grundlage
11. Automatische Navigation aus dem nav-Ordner
12. Modernes responsives Grundlayout
13. Light Mode und Dark Mode
14. Dashboard-Grundseite
15. Benutzerübersicht
16. Benutzeraktivitäten
17. Loginprotokoll
18. Sessionübersicht
19. Agentenübersicht
20. API-Benutzerübersicht
21. MCP-Benutzerübersicht
22. System- und Datenbankfehlerprotokoll
23. Zentrale Einstellungen
24. Zentralen API-Endpunkt api.php
25. Zentralen MCP-Endpunkt mcp.php
26. Health- beziehungsweise Statusabfrage für API und MCP
27. Fehlerbehandlung und Logging
28. Grundlegende Tests

Entwickle keinen unechten Demo-Code.

Die Anwendung muss tatsächlich starten, die Datenbank verwenden und eine Anmeldung ermöglichen.

============================================================
5. VERZEICHNISSTRUKTUR
============================================================

Erstelle beziehungsweise vervollständige die dokumentierte Verzeichnisstruktur.

Mindestens erforderlich:

/
├── index.php
├── login.php
├── logout.php
├── api.php
├── mcp.php
├── .htaccess
│
├── css/
├── fonts/
├── img/
├── inc/
├── js/
├── logs/
├── nav/
├── sql/
├── uploads/
├── vendor/
└── docs/

Wichtige Regeln:

- api.php ist der einzige öffentliche API-Einstiegspunkt.
- mcp.php ist der einzige öffentliche MCP-Einstiegspunkt.
- Es werden keine zusätzlichen öffentlichen API-Endpunkte angelegt.
- Es werden keine zusätzlichen öffentlichen MCP-Endpunkte angelegt.
- Inhaltsseiten liegen unter nav/.
- Wiederverwendbare Business-Logik liegt unter inc/.
- SQL-Dateien liegen unter sql/.
- Statische Bilder liegen unter img/.
- Benutzeruploads liegen unter uploads/.
- Entwicklungsdokumentation wird nicht gelöscht.

============================================================
6. ZENTRALE KONFIGURATION
============================================================

Erstelle eine zentrale Konfigurationsstruktur.

Sie muss mindestens enthalten:

- Systemname
- Kurzname
- Untertitel
- Beschreibung
- Sprache
- Zeitzone
- Basis-URL
- Test-URL für automatisierte Browser- und HTTP-Tests
- Theme
- Logo
- Favicon
- Datenbankverbindung
- Entwicklungsmodus
- Debugmodus
- sichere Pfaddefinitionen

Geheimnisse dürfen nicht in einer öffentlich lesbaren Datei ausgegeben werden.

Falls die Konfiguration innerhalb des Webroots gespeichert werden muss:

- lege sie ausschließlich unter inc/ ab,
- sperre den direkten HTTP-Zugriff,
- ergänze passende Apache-Regeln,
- gib niemals Konfigurationsinhalte im Browser aus.

Erstelle keine Zugangsdaten in JavaScript.

============================================================
7. FUNKTIONSLOADER
============================================================

Erstelle:

inc/funktionen.php

Diese Datei lädt automatisch alle Dateien nach folgendem Muster:

inc/funktionen_*.php

Regeln:

- funktionen.php darf sich nicht selbst rekursiv laden.
- Lade Funktionsdateien in stabiler alphabetischer Reihenfolge.
- Verwende require_once.
- Prüfe, dass jede Datei nur einmal geladen wird.
- Fehler beim Laden werden im system_log beziehungsweise in einer sicheren Fallback-Logdatei protokolliert.
- Keine manuelle Liste aller Funktionsdateien pflegen.

============================================================
8. FUNKTIONSMODULE
============================================================

Erstelle mindestens die notwendigen technischen Funktionsmodule:

inc/funktionen_datenbank.php
inc/funktionen_sicherheit.php
inc/funktionen_validierung.php
inc/funktionen_session.php
inc/funktionen_navigation.php
inc/funktionen_api.php
inc/funktionen_mcp.php
inc/funktionen_system.php
inc/funktionen_einstellungen.php

Erstelle für die Core-Tabellen jeweils die passende Funktionsdatei:

inc/funktionen_benutzer.php
inc/funktionen_benutzer_gruppen.php
inc/funktionen_benutzer_gruppen_zuordnung.php
inc/funktionen_rechte.php
inc/funktionen_benutzer_gruppen_rechte.php
inc/funktionen_benutzer_log.php
inc/funktionen_benutzer_logins.php
inc/funktionen_sessions.php
inc/funktionen_api_benutzer.php
inc/funktionen_api_log.php
inc/funktionen_mcp_benutzer.php
inc/funktionen_mcp_log.php
inc/funktionen_agents.php
inc/funktionen_agents_log.php
inc/funktionen_db_fehler_log.php
inc/funktionen_system_log.php

Regeln:

- Jede Tabellenlogik liegt in genau einer fachlich passenden Funktionsdatei.
- Allgemeine technische Logik liegt in technischen Funktionsmodulen.
- Keine doppelte Business-Logik.
- Keine HTML-Ausgabe in Business-Funktionen.
- Keine fertigen JSON-Antworten in Business-Funktionen.
- Keine fertigen MCP-Antworten in Business-Funktionen.
- Keine SQL-Abfragen in nav-Dateien.
- Keine SQL-Abfragen in index.php.
- Keine fachlichen SQL-Abfragen in api.php oder mcp.php.
- Web, API und MCP verwenden dieselben zentralen Funktionen.

============================================================
9. DATENBANK
============================================================

Verwende MariaDB mit:

- InnoDB
- utf8mb4
- PDO
- vorbereiteten Statements
- Exceptions
- Transaktionen bei zusammenhängenden Änderungen

Erstelle:

sql/schema_core.sql
sql/basisdaten.sql
sql/migrations/

Erstelle die Core-Tabellen entsprechend der Dokumentation:

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

Verbindliche Datenbankregel:

- Verwende kein NULL.
- Integer und optionale IDs verwenden 0.
- Texte verwenden ''.
- JSON-Objekte verwenden '{}'.
- JSON-Listen verwenden '[]'.
- Zeitwerte verwenden BIGINT UNSIGNED als Unix-Timestamp.
- Der Zeitwert 0 bedeutet nicht gesetzt.
- Boolean-Werte verwenden TINYINT(1) mit 0 oder 1.

Wichtig bei optionalen Beziehungen:

- Ein Wert von 0 bedeutet keine Zuordnung.
- Lege für optionale 0-Beziehungen keine fehlerhaften Fremdschlüssel an.
- Prüfe optionale Beziehungen in der Business-Logik.
- Pflichtbeziehungen dürfen mit echten Fremdschlüsseln abgesichert werden.
- Verwende sinnvolle Indizes und eindeutige Schlüssel.
- Verwende keine ungültigen MariaDB-Zero-Dates.

============================================================
10. DATENBANKINITIALISIERUNG
============================================================

Erstelle eine kontrollierte Initialisierung.

Die Initialisierung muss:

- prüfen, ob Tabellen bereits existieren,
- nur fehlende Tabellen anlegen,
- nur fehlende Basisdaten ergänzen,
- keine vorhandenen Datensätze überschreiben,
- keine Tabellen löschen,
- mehrfach ausführbar sein,
- nach erfolgreicher Einrichtung nicht ungeschützt öffentlich erreichbar bleiben.

Lege Basisdaten an:

Benutzergruppen:

- Administrator
- Entwickler
- Benutzer
- Gast

Erforderliche Core-Rechte, mindestens:

- dashboard_anzeigen
- benutzer_anzeigen
- benutzer_anlegen
- benutzer_bearbeiten
- benutzer_deaktivieren
- benutzer_gruppen_verwalten
- rechte_verwalten
- sessions_anzeigen
- sessions_beenden
- benutzer_log_anzeigen
- benutzer_logins_anzeigen
- agents_anzeigen
- agents_bearbeiten
- agents_starten
- agents_stoppen
- api_benutzer_anzeigen
- api_benutzer_verwalten
- api_log_anzeigen
- mcp_benutzer_anzeigen
- mcp_benutzer_verwalten
- mcp_log_anzeigen
- system_log_anzeigen
- db_fehler_log_anzeigen
- einstellungen_anzeigen
- einstellungen_bearbeiten

Ordne alle Core-Rechte der Administratorgruppe zu.

Lege den ersten Administrator an:

Benutzername:
{{ERSTER_ADMIN_BENUTZERNAME}}

E-Mail:
{{ERSTER_ADMIN_EMAIL}}

Passwort:
{{ERSTER_ADMIN_PASSWORT}}

Regeln:

- Speichere ausschließlich den Passwort-Hash.
- Verwende password_hash().
- Prüfe vor dem Anlegen, ob der Benutzer bereits existiert.
- Überschreibe niemals ein vorhandenes Passwort.
- Protokolliere das Klartextpasswort nicht.
- Gib das Passwort nach der Initialisierung nicht erneut aus.
- Weise den Benutzer der Administratorgruppe zu.

============================================================
11. LOGIN UND SESSION
============================================================

Entwickle einen sicheren Login.

Erforderlich:

- login.php
- logout.php
- Benutzername oder E-Mail
- password_verify()
- CSRF-Schutz
- sichere Session-Cookies
- Session-Regeneration nach erfolgreichem Login
- Session-Timeout
- Speicherung der Session in der Tabelle sessions
- Loginprotokoll in benutzer_logins
- Aktivitätsprotokoll in benutzer_log
- Sperrung inaktiver oder gesperrter Benutzer
- neutrale Fehlermeldungen
- Schutz gegen unbegrenzte Loginversuche

Session-Cookie mindestens:

- httponly
- samesite=lax oder strenger
- secure bei HTTPS
- kontrollierte Lebensdauer

Die echte Session-ID darf nicht im Klartext in der Datenbank gespeichert werden.

Speichere ausschließlich einen sicheren Hash.

============================================================
12. RECHTEPRÜFUNG
============================================================

Prüfe immer konkrete Rechte, nicht Gruppennamen.

Beispiel:

recht_pruefen('agents_starten')

Nicht:

if ($gruppe === 'Administrator')

Die Rechteprüfung muss:

- zentral implementiert sein,
- in Weboberfläche, API und MCP funktionieren,
- auch innerhalb geschäftskritischer Funktionen erneut geprüft werden,
- serverseitig verbindlich sein.

Nicht erlaubte Navigationseinträge und Aktionen werden nicht angezeigt.

Die serverseitige Prüfung bleibt trotzdem zwingend.

============================================================
13. NAVIGATION
============================================================

Erzeuge die Navigation automatisch aus den Dateien unter nav/.

Verwende die dokumentierten Metadaten:

ICON
TITLE
PRIO
BEREICH
RECHT

Hauptbereiche:

Main
Work
Control
Settings

Erstelle mindestens folgende Seiten:

nav/main_home.php
nav/work_agents.php
nav/control_benutzer_log.php
nav/control_benutzer_logins.php
nav/control_sessions.php
nav/control_api_log.php
nav/control_mcp_log.php
nav/control_system_log.php
nav/control_db_fehler_log.php
nav/settings_benutzer.php
nav/settings_benutzer_gruppen.php
nav/settings_api_benutzer.php
nav/settings_mcp_benutzer.php
nav/settings_einstellungen.php

Regeln:

- Neue Datei im nav-Ordner erzeugt automatisch einen Menüpunkt.
- Navigation wird nach PRIO sortiert.
- Navigation wird nach BEREICH gruppiert.
- Navigation prüft RECHT.
- Aktive Seite wird hervorgehoben.
- Ungültige Seitenparameter dürfen keine beliebigen Dateien laden.
- Verwende eine Whitelist aus den tatsächlich eingelesenen Navigationsdateien.
- Verhindere Directory Traversal und freie include-Pfade.

============================================================
14. SEITENAUFBAU
============================================================

index.php ist der zentrale Einstiegspunkt der Weboberfläche.

Verbindlicher Ablauf:

1. Konfiguration laden
2. Fehlerbehandlung initialisieren
3. Datenbank verbinden
4. Session starten
5. Funktionen laden
6. Loginstatus prüfen
7. Seite und Rechte prüfen
8. Header laden
9. Navigation laden
10. Inhaltsseite laden
11. Footer laden

index.php enthält keine fachliche Business-Logik.

Erstelle zentrale Layoutdateien:

inc/header.php
inc/navigation.php
inc/meldungen.php
inc/footer.php

============================================================
15. DESIGN
============================================================

Setze das Design entsprechend den Dokumenten 13 bis 16 um.

Erstelle mindestens:

css/tokens.css
css/reset.css
css/basis.css
css/layout.css
css/navigation.css
css/komponenten.css
css/dashboard.css
css/formulare.css
css/tabellen.css
css/hilfsklassen.css
css/responsive.css
css/druck.css

Verwende die Projektvariablen für die Kernfarben.

Erstelle zentrale CSS Custom Properties für:

- Primärfarbe
- Sekundärfarbe
- Akzentfarbe
- Erfolg
- Warnung
- Fehler
- Information
- Hintergründe
- Flächen
- Texte
- Rahmen
- Abstände
- Radien
- Schatten
- Typografie
- Z-Index
- Animationen

Die Anwendung muss modern, ruhig und professionell wirken.

Verbindlich:

- fester Header
- einklappbare Sidebar
- automatische Navigation
- einheitlicher Seitenkopf
- Karten
- Status-Badges
- Toolbars
- moderne Formulare
- moderne Tabellen
- responsive Darstellung
- Light Mode
- Dark Mode
- sichtbare Fokuszustände
- Empty States
- Ladezustände
- Fehlerzustände
- barrierearme Bedienung

Verwende keine externen UI-Frameworks ohne ausdrückliche Notwendigkeit.

Verwende keine Mischung aus mehreren Iconsets.

Falls kein Iconset lokal vorhanden ist, verwende zunächst einfache, konsistente Inline-SVG-Icons oder eine klar dokumentierte lokale Lösung.

============================================================
16. DASHBOARD
============================================================

Erstelle eine funktionsfähige Dashboard-Startseite.

Zeige mindestens:

- Systemname
- Systemstatus
- Datenbankstatus
- Anzahl aktiver Benutzer
- Anzahl aktiver Sessions
- Anzahl Agent-Server
- Anzahl Sub-Agenten
- Anzahl API-Benutzer
- Anzahl MCP-Benutzer
- API-Aufrufe der letzten 24 Stunden
- MCP-Aufrufe der letzten 24 Stunden
- letzte Benutzeraktivitäten
- letzte Systemfehler

Falls noch keine Daten vorhanden sind, verwende sinnvolle Empty States.

Keine erfundenen Kennzahlen anzeigen.

============================================================
17. BENUTZERBEREICH
============================================================

Erstelle mindestens:

- Benutzerübersicht
- Benutzer anlegen
- Benutzer bearbeiten
- Benutzer aktivieren und deaktivieren
- Benutzer sperren und entsperren
- Benutzergruppen zuordnen
- Passwort setzen beziehungsweise ändern
- Aktivitätsansicht
- Loginansicht
- Sessionansicht

Beachte vollständig:

15_Formular_Standards.md
16_Tabellen_Standards.md

============================================================
18. AGENTENBEREICH
============================================================

Die Tabelle agents enthält:

- Agent-Server
- Sub-Agenten

Regel:

- Agent-Server: haupt_id = 0
- Sub-Agent: haupt_id = ID des Agent-Servers

Erstelle eine Übersicht, in der die Hierarchie klar sichtbar ist.

Mindestens anzeigen:

- Bezeichnung
- Typ
- Haupt-Agent
- Hostname
- IP-Adresse
- Port
- Protokoll
- Status
- Aktiv
- letzte Aktivität
- Aktionen

Implementiere zunächst sichere Grundfunktionen zum:

- Anzeigen
- Anlegen
- Bearbeiten
- Aktivieren
- Deaktivieren

Start- und Stop-Aktionen dürfen nur als echte Funktion implementiert werden, wenn eine dokumentierte technische Schnittstelle vorhanden ist.

Erfinde keine nicht vorhandene Agenten-API.

Falls eine echte Start-/Stop-Schnittstelle fehlt, zeige die Aktionen deaktiviert mit verständlichem Hinweis.

============================================================
19. EINSTELLUNGEN
============================================================

Implementiere die Tabelle einstellungen über:

inc/funktionen_einstellungen.php

Die zentrale Datei muss mindestens bereitstellen:

einstellung_laden()
einstellung_speichern()
einstellung_loeschen()
einstellung_existiert()
einstellungen_array()
einstellung_json_laden()
einstellung_json_speichern()

Verwende Gültigkeitsbereiche wie:

system
benutzer:15
api:3
mcp:7
agent:12

Implementiere den Fallback:

1. spezifischer Wert
2. systemweiter Wert
3. Standardwert aus dem Code

Keine direkten SQL-Zugriffe auf einstellungen außerhalb von funktionen_einstellungen.php.

Speichere mindestens die Projektwerte als globale Systemeinstellungen, soweit dies fachlich sinnvoll ist.

Geheimnisse wie Datenbankpasswörter gehören nicht in die allgemeine Einstellungstabelle.

============================================================
20. API
============================================================

api.php ist der einzige öffentliche API-Endpunkt.

Implementiere mindestens:

- zentrale Request-Verarbeitung
- JSON-Eingabe
- JSON-Ausgabe
- Authentifizierung
- Rechteprüfung
- Validierung
- Routing
- Logging in api_log
- strukturierte Fehlerausgabe
- Request-ID
- Laufzeitmessung
- Health- beziehungsweise Statusabfrage

Standardantwort:

{
    "erfolg": true,
    "meldung": "",
    "daten": {},
    "request_id": ""
}

Fehler:

{
    "erfolg": false,
    "meldung": "Verständliche Fehlermeldung",
    "daten": {},
    "request_id": ""
}

Keine technischen SQL- oder PHP-Fehler an den Client ausgeben.

Implementiere keine separate Fachlogik in api.php.

============================================================
21. MCP
============================================================

mcp.php ist der einzige öffentliche MCP-Endpunkt.

Implementiere einen sauberen, erweiterbaren MCP-Grundendpunkt entsprechend der vorhandenen Dokumentation.

Mindestens erforderlich:

- Request-Verarbeitung
- Authentifizierung über MCP-Benutzer
- Rechteprüfung
- Request-ID
- Logging in mcp_log
- verständliche Fehlerbehandlung
- initialize
- tools/list
- resources/list
- sichere Status- beziehungsweise Health-Funktion

Verwende ausschließlich zentrale Business-Funktionen.

Erfinde keine fachlichen Tools ohne vorhandene Implementierung.

Definiere zunächst sichere Core-Tools beziehungsweise Resources, beispielsweise:

- Systemstatus lesen
- Agentenübersicht lesen

Schreibende Tools werden erst freigeschaltet, wenn Rechteprüfung, Validierung und konkrete Business-Funktion vorhanden sind.

============================================================
22. LOGGING
============================================================

Implementiere zentrale Logging-Funktionen.

Verwende:

benutzer_log
benutzer_logins
api_log
mcp_log
agents_log
db_fehler_log
system_log

Logge keine Geheimnisse.

Maskiere mindestens:

- passwort
- passwort_hash
- api_key
- mcp_key
- schluessel
- schluessel_hash
- token
- authorization
- secret
- cookie
- session_id

Falls die Datenbank selbst nicht erreichbar ist:

- verwende eine minimale sichere Fallback-Datei unter logs/,
- speichere keine Geheimnisse,
- verhindere direkten Browserzugriff auf logs/.

============================================================
23. FEHLERBEHANDLUNG
============================================================

Entwicklungsmodus und Browser-Debugausgabe sind getrennt.

Bei:

DEBUG_AUSGABE_IM_BROWSER=0

werden technische Fehler niemals im Browser angezeigt.

Stattdessen:

- neutrale Meldung an den Benutzer,
- vollständige technische Protokollierung,
- Request-ID zur Zuordnung.

Fange mindestens ab:

- PDOException
- allgemeine Throwable
- ungültige Requests
- fehlende Rechte
- CSRF-Fehler
- Sessionfehler
- JSON-Fehler
- Datei- und Include-Fehler

============================================================
24. SICHERHEIT
============================================================

Verbindlich:

- PDO Prepared Statements
- password_hash()
- password_verify()
- CSRF-Schutz
- XSS-Schutz
- sichere Sessions
- Rechteprüfung
- Request-Validierung
- sichere Dateipfade
- sichere Uploadregeln
- keine PHP-Ausführung unter uploads/
- kein Browserzugriff auf logs/
- kein Browserzugriff auf interne inc-Dateien
- keine Directory Listings
- keine Klartextschlüssel
- keine geheimen Werte im Frontend
- keine technischen Fehlermeldungen im Browser

Erstelle passende .htaccess-Regeln für Apache.

Achte darauf, dass api.php und mcp.php erreichbar bleiben.

============================================================
25. DATEIRECHTE
============================================================

Die Projektvorgabe lautet:

- Besitzer: www-data:www-data
- Ordner: 755
- Dateien: 755

Setze diese Rechte nur, wenn dies über den Mount sicher und erlaubt möglich ist.

Verwende keine rekursiven Eigentümer- oder Rechteänderungen außerhalb von {{PROJEKT_MOUNT}}.

Falls chown über den Mount nicht möglich ist:

- brich die Entwicklung nicht ab,
- dokumentiere die betroffenen Dateien,
- gib am Ende den exakten Befehl aus, der auf dem Debian-Webserver ausgeführt werden muss.

Beispiel:

chown -R www-data:www-data {{ZIEL_WEBROOT}}
find {{ZIEL_WEBROOT}} -type d -exec chmod 755 {} \;
find {{ZIEL_WEBROOT}} -type f -exec chmod 755 {} \;

============================================================
26. TEST-URL DES AGENTEN
============================================================

Für Browser- und HTTP-Tests steht dem Agenten folgende URL zur Verfügung:

AGENT_TEST_URL={{AGENT_TEST_URL}}

Der Benutzer ersetzt `XX` vor der Ausführung durch die tatsächliche letzte Stelle beziehungsweise den tatsächlichen Hostanteil der IP-Adresse des Debian-Webservers.

Beispiel:

http://192.168.178.45

Verbindliche Regeln:

- Verwende {{AGENT_TEST_URL}} für alle HTTP- und Browserprüfungen.
- Verwende nicht automatisch localhost oder 127.0.0.1, da OpenClaw auf einem anderen Server arbeitet.
- Der Quellcode wird unter {{PROJEKT_MOUNT}} bearbeitet.
- Die laufende Anwendung wird über {{AGENT_TEST_URL}} auf dem Debian-Webserver geprüft.
- Prüfe vor den Funktionstests, ob {{AGENT_TEST_URL}} erreichbar ist.
- Prüfe anschließend den HTTP-Statuscode und die ausgelieferte Anwendung.
- Hardcode {{AGENT_TEST_URL}} niemals in PHP-, CSS- oder JavaScript-Dateien.
- Die Anwendung muss später auch unter einer anderen IP-Adresse oder Domain funktionieren.
- Verwende die Test-URL ausschließlich als Laufzeitadresse für Entwicklungs- und Funktionstests.

Zu testende URLs:

```text
Weboberfläche:
{{AGENT_TEST_URL}}/

Login:
{{AGENT_TEST_URL}}/login.php

API:
{{AGENT_TEST_URL}}/api.php

MCP:
{{AGENT_TEST_URL}}/mcp.php
```

Empfohlene Erreichbarkeitsprüfung:

```bash
curl -I --max-time 10 "{{AGENT_TEST_URL}}/"
```

Empfohlene Prüfung der Weboberfläche:

```bash
curl -sS --max-time 10 "{{AGENT_TEST_URL}}/" \
    -o /tmp/noobclaw_startseite.html
```

Empfohlene API-Statusprüfung:

```bash
curl -sS --max-time 10 \
    "{{AGENT_TEST_URL}}/api.php?modul=system&aktion=status"
```

Empfohlene MCP-Initialisierungsprüfung:

```bash
curl -sS --max-time 10 \
    -X POST \
    -H "Content-Type: application/json" \
    --data '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{}}' \
    "{{AGENT_TEST_URL}}/mcp.php"
```

Authentifizierte Tests dürfen erst durchgeführt werden, nachdem passende Testzugänge kontrolliert angelegt wurden.

Falls {{AGENT_TEST_URL}} nicht erreichbar ist:

- brich nicht sofort die komplette Entwicklung ab,
- prüfe Mount, Dateien, PHP-Syntax und Datenbank trotzdem,
- dokumentiere die fehlende Erreichbarkeit,
- nenne mögliche Ursachen wie Apache, VirtualHost, Firewall, Mount oder Dateirechte,
- führe keine erfundenen erfolgreichen HTTP-Tests im Abschlussbericht auf.

============================================================
27. TESTS
============================================================

Führe nach der Entwicklung mindestens folgende Prüfungen aus:

1. PHP-Syntaxprüfung aller PHP-Dateien
2. Prüfung des Funktionsloaders
3. Datenbankverbindung
4. Vorhandensein aller Core-Tabellen
5. Login mit dem initialen Administrator
6. Logout
7. Sessionanlage
8. Sessionbeendigung
9. Rechteprüfung
10. Navigation
11. Light Mode
12. Dark Mode
13. API-Health
14. MCP-Initialize beziehungsweise MCP-Status
15. Fehlerlogging
16. CSRF-Schutz
17. ungültige Loginversuche
18. nicht autorisierte API-Anfrage
19. nicht autorisierte MCP-Anfrage
20. responsive Grunddarstellung

Führe mindestens aus:

find {{PROJEKT_MOUNT}} -type f -name '*.php' -print0 | xargs -0 -n1 php -l

Führe kontrollierte HTTP- und Funktionstests über {{AGENT_TEST_URL}} durch. Falls die URL nicht erreichbar ist, dokumentiere dies und führe alle lokal möglichen Prüfungen trotzdem aus.

Lösche keine Daten, um Tests zu vereinfachen.

============================================================
28. ARBEITSWEISE
============================================================

Arbeite in klaren Phasen:

Phase 1:
Dokumentation lesen und Bestand analysieren

Phase 2:
Verzeichnisstruktur und Bootstrap

Phase 3:
Datenbank und Initialisierung

Phase 4:
Login, Sessions, Benutzer und Rechte

Phase 5:
Navigation und Grundlayout

Phase 6:
Dashboard und Core-Seiten

Phase 7:
API und MCP

Phase 8:
Logging und Fehlerbehandlung

Phase 9:
Tests und Korrekturen

Phase 10:
Abschlussbericht

Stelle nicht nach jeder kleinen Datei Rückfragen.

Arbeite selbstständig weiter, solange:

- keine Sicherheitsentscheidung offen ist,
- keine destruktive Änderung notwendig ist,
- alle benötigten Werte vorhanden sind.

============================================================
29. QUALITÄTSREGELN
============================================================

Verbindlich:

- kleine, klar benannte Funktionen
- keine doppelten Funktionen
- keine Copy-and-Paste-Business-Logik
- sprechende deutsche Namen
- technische Standardbegriffe dürfen englisch bleiben
- keine riesigen Dateien ohne fachlichen Grund
- keine ungenutzten Funktionen
- keine toten Variablen
- keine Hardcodierung von Projektfarben
- keine Hardcodierung von Zugangsdaten
- keine direkten SQL-Abfragen in Seiten
- keine HTML-Ausgabe in Business-Funktionen
- keine ungeprüften Request-Parameter
- keine erfundenen Schnittstellen
- keine Mock-Daten als echte Systemdaten
- keine unvollständigen Sicherheitsprüfungen

============================================================
30. ABSCHLUSSBERICHT
============================================================

Erstelle nach Abschluss einen klar strukturierten Bericht.

Der Bericht enthält:

1. Zusammenfassung
2. Angelegte Verzeichnisse
3. Angelegte Dateien
4. Angelegte Tabellen
5. Angelegte Basisdaten
6. Implementierte Funktionen
7. Implementierte Seiten
8. Implementierte API-Funktionen
9. Implementierte MCP-Funktionen
10. Durchgeführte Tests
11. Testergebnisse
12. Offene Punkte
13. Bewusste Abweichungen von der Dokumentation
14. Sicherheitsrelevante Hinweise
15. Noch auszuführende Serverbefehle
16. Verwendete Test-URL
17. Ergebnis der Erreichbarkeits- und HTTP-Prüfung
18. URL für den ersten Login
19. Hinweis zum Ändern des initialen Admin-Passworts

Gib keine geheimen Passwörter oder Schlüssel im Abschlussbericht aus.

============================================================
31. START
============================================================

Beginne jetzt.

Prüfe zuerst:

- ob {{PROJEKT_MOUNT}} existiert,
- ob der Mount beschreibbar ist,
- welche Markdown-Dateien vorhanden sind,
- welche Projektdateien bereits existieren,
- ob PHP verfügbar ist,
- ob die MariaDB-Verbindung hergestellt werden kann,
- ob {{AGENT_TEST_URL}} erreichbar ist.

Lies danach die gesamte Dokumentation und starte anschließend selbstständig mit Phase 1 bis Phase 10.
```

---

# Verwendung des Prompts

Vor dem Einfügen in OpenClaw werden die Platzhalter der Form:

```text
{{VARIABLE}}
```

durch die Werte aus dem Variablenblock ersetzt.

Beispiel:

```text
{{SYSTEM_NAME}}
```

wird zu:

```text
NoobClaw
```

Alternativ kann der gesamte Variablenblock gemeinsam mit dem Prompt an den Agenten übergeben werden, wenn der Agent die Variablen zuverlässig selbst auflösen kann.

---

# Empfohlener Umfang der ersten Entwicklung

Dieser Prompt startet bewusst nicht sofort mit sämtlichen späteren Fachmodulen.

Der erste Entwicklungsstand konzentriert sich auf:

- Architektur,
- Datenbank,
- Benutzer,
- Rechte,
- Sessions,
- Navigation,
- Design,
- Agenten-Grundverwaltung,
- API-Grundstruktur,
- MCP-Grundstruktur,
- Logging,
- Einstellungen.

Weitere Funktionen wie:

- Modellverwaltung,
- Prompts,
- Workflows,
- Scheduler,
- Agentenkommunikation,
- Skills,
- externe APIs,
- vollständige MCP-Tool-Sammlung

werden danach als eigene Entwicklungsschritte ergänzt.
