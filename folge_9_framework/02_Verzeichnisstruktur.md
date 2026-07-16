# Verzeichnisstruktur – NoobClaw Webinterface

## 1. Ziel der Verzeichnisstruktur

Die Verzeichnisstruktur bildet das technische Grundgerüst des NoobClaw-Webinterfaces.

Sie soll sicherstellen, dass:

- jeder Ordner genau eine klar definierte Aufgabe besitzt,
- PHP-Logik, Darstellung, Schnittstellen und Dateien sauber getrennt sind,
- neue Module ohne Umbau der Grundstruktur ergänzt werden können,
- der KI-Agent bestehende Funktionen wiederverwendet,
- keine unkontrollierten Direktzugriffe auf Datenbank, API oder MCP entstehen,
- die Anwendung langfristig wartbar und erweiterbar bleibt.

Projektpfad auf dem Webserver:

```text
/var/www/noobclaw/
```

---

# 2. Vollständige Grundstruktur

```text
/var/www/noobclaw/
│
├── index.php
├── login.php
├── logout.php
├── .htaccess
│
├── api/
│   ├── index.php
│   ├── benutzer.php
│   ├── agenten.php
│   ├── system.php
│   └── status.php
│
├── css/
│   ├── style.css
│   ├── layout.css
│   ├── navigation.css
│   ├── formulare.css
│   ├── tabellen.css
│   ├── komponenten.css
│   └── responsive.css
│
├── fonts/
│   └── README.md
│
├── img/
│   ├── logo/
│   ├── icons/
│   ├── backgrounds/
│   └── placeholders/
│
├── inc/
│   ├── config.php
│   ├── datenbank.php
│   ├── session.php
│   ├── funktionen.php
│   │
│   ├── funktionen_api.php
│   ├── funktionen_agenten.php
│   ├── funktionen_benutzer.php
│   ├── funktionen_login.php
│   ├── funktionen_logs.php
│   ├── funktionen_mcp.php
│   ├── funktionen_navigation.php
│   ├── funktionen_rechte.php
│   ├── funktionen_system.php
│   ├── funktionen_validierung.php
│   │
│   ├── header.php
│   ├── navigation.php
│   ├── meldungen.php
│   └── footer.php
│
├── js/
│   ├── app.js
│   ├── navigation.js
│   ├── formulare.js
│   ├── tabellen.js
│   ├── ajax.js
│   └── dashboard.js
│
├── logs/
│   ├── .htaccess
│   ├── application.log
│   ├── error.log
│   ├── api.log
│   ├── mcp.log
│   └── security.log
│
├── mcp/
│   ├── index.php
│   ├── server.php
│   ├── tools.php
│   └── resources.php
│
├── nav/
│   ├── main_home.php
│   ├── work_testbereich.php
│   ├── control_logfile.php
│   └── settings_verwaltung.php
│
├── sql/
│   ├── schema.sql
│   ├── basisdaten.sql
│   ├── testdaten.sql
│   └── migrations/
│       └── README.md
│
├── uploads/
│   ├── .htaccess
│   ├── benutzer/
│   ├── agenten/
│   ├── dokumente/
│   └── temporaer/
│
├── vendor/
│   └── ...
│
└── docs/
    ├── 01-grundentwicklung.md
    ├── 02-datenbankschema.md
    ├── 03-coding-rules.md
    ├── 04-verzeichnisstruktur.md
    ├── 05-navigation.md
    ├── 06-api-konzept.md
    ├── 07-mcp-konzept.md
    └── 08-sicherheitskonzept.md
```

---

# 3. Root-Verzeichnis

Das Root-Verzeichnis enthält ausschließlich zentrale Einstiegspunkte und technische Steuerdateien.

## `index.php`

Zentraler Einstiegspunkt der Anwendung.

Aufgaben:

- Konfiguration laden,
- Datenbankverbindung herstellen,
- Session starten,
- Funktionen laden,
- Loginstatus prüfen,
- Navigation laden,
- gewünschte Inhaltsseite ausgeben,
- Header und Footer einbinden.

`index.php` darf keine umfangreiche Fachlogik enthalten.

## `login.php`

Eigenständige Loginseite.

Aufgaben:

- Loginformular anzeigen,
- Zugangsdaten prüfen,
- Session anlegen,
- erfolgreichen oder fehlgeschlagenen Login protokollieren.

## `logout.php`

Beendet die Benutzersitzung und leitet zurück zur Anmeldung.

## `.htaccess`

Zentrale Apache-Regeln.

Mögliche Aufgaben:

- Verzeichnisschutz,
- Weiterleitungen,
- Zugriffsschutz für interne Dateien,
- Sicherheitsheader,
- saubere URLs,
- Blockierung unerwünschter Dateitypen.

---

# 4. Ordner `/api`

Der Ordner enthält ausschließlich HTTP-Endpunkte für interne oder externe API-Zugriffe.

```text
/api/
├── index.php
├── benutzer.php
├── agenten.php
├── system.php
└── status.php
```

Regeln:

- API-Dateien enthalten keine eigenständige Datenbanklogik.
- API-Dateien verwenden ausschließlich zentrale Funktionen aus `/inc`.
- Antworten werden einheitlich als JSON zurückgegeben.
- Jeder Zugriff wird authentifiziert und protokolliert.
- Eingaben werden vor der Verarbeitung validiert.
- Fehler werden intern protokolliert und extern neutral ausgegeben.

Beispiele:

- `/api/benutzer.php`
- `/api/agenten.php`
- `/api/status.php`

---

# 5. Ordner `/css`

Der Ordner enthält ausschließlich Stylesheets.

```text
/css/
├── style.css
├── layout.css
├── navigation.css
├── formulare.css
├── tabellen.css
├── komponenten.css
└── responsive.css
```

## Aufteilung

### `style.css`

Globale Designgrundlagen:

- Farben,
- Schriften,
- Hintergrund,
- Standardabstände,
- Grundelemente.

### `layout.css`

Aufbau der Anwendung:

- Header,
- Sidebar,
- Hauptbereich,
- Footer,
- Grid,
- Seitenbreiten.

### `navigation.css`

Darstellung der Haupt- und Unternavigation.

### `formulare.css`

Formularelemente:

- Eingabefelder,
- Select-Felder,
- Checkboxen,
- Buttons,
- Validierungszustände.

### `tabellen.css`

Tabellen, Listenansichten und Pagination.

### `komponenten.css`

Wiederverwendbare UI-Bausteine:

- Karten,
- Statusanzeigen,
- Hinweise,
- Modale Fenster,
- Badges.

### `responsive.css`

Anpassungen für:

- Desktop,
- Tablet,
- Smartphone.

---

# 6. Ordner `/fonts`

Enthält lokale Schriftdateien, sofern diese später verwendet werden.

```text
/fonts/
└── README.md
```

Regeln:

- Keine Schriftdateien ohne geklärte Lizenz einbinden.
- Schriftdefinitionen erfolgen zentral über CSS.
- Externe Abhängigkeiten möglichst vermeiden.

---

# 7. Ordner `/img`

Enthält ausschließlich statische Bilddateien.

```text
/img/
├── logo/
├── icons/
├── backgrounds/
└── placeholders/
```

## Unterordner

### `/img/logo`

Logos der Anwendung und ihrer Komponenten.

### `/img/icons`

Eigene Icons oder lokale Iconsets.

### `/img/backgrounds`

Hintergründe und dekorative Grafiken.

### `/img/placeholders`

Platzhalterbilder für Benutzer, Agenten oder fehlende Inhalte.

Benutzeruploads gehören nicht in `/img`, sondern in `/uploads`.

---

# 8. Ordner `/inc`

Der Ordner `/inc` enthält die komplette zentrale technische Logik der Anwendung.

```text
/inc/
├── config.php
├── datenbank.php
├── session.php
├── funktionen.php
│
├── funktionen_api.php
├── funktionen_agenten.php
├── funktionen_benutzer.php
├── funktionen_login.php
├── funktionen_logs.php
├── funktionen_mcp.php
├── funktionen_navigation.php
├── funktionen_rechte.php
├── funktionen_system.php
├── funktionen_validierung.php
│
├── header.php
├── navigation.php
├── meldungen.php
└── footer.php
```

## `config.php`

Enthält zentrale Konfigurationen.

Beispiele:

- Anwendungsname,
- Basispfad,
- Basis-URL,
- Datenbankparameter,
- Debugmodus,
- Sessionkonfiguration,
- Uploadgrenzen,
- API-Einstellungen,
- MCP-Einstellungen.

Zugangsdaten sollen später bevorzugt aus einer nicht öffentlich erreichbaren Konfigurationsdatei oder aus Umgebungsvariablen geladen werden.

## `datenbank.php`

Stellt die zentrale Datenbankverbindung bereit.

Regeln:

- genau eine zentrale Verbindungslogik,
- Verwendung von PDO,
- UTF-8 beziehungsweise `utf8mb4`,
- Exceptions kontrolliert behandeln,
- keine Ausgabe technischer Zugangsdaten.

## `session.php`

Verantwortlich für:

- sicheren Sessionstart,
- Session-Cookie-Einstellungen,
- Session-Zeitlimits,
- Session-Regeneration,
- Prüfung angemeldeter Benutzer.

## `funktionen.php`

Zentraler Loader für alle Funktionsdateien.

Er lädt automatisch alle Dateien nach folgendem Muster:

```text
funktionen_*.php
```

Dadurch müssen neue Funktionsmodule nicht manuell in jeder Seite eingebunden werden.

## Funktionsdateien

### `funktionen_api.php`

Zentrale Funktionen für API-Ausgaben und API-Aufrufe.

### `funktionen_agenten.php`

Funktionen für Agenten.

Beispiele:

- `agenten_array()`
- `agent_laden()`
- `agent_speichern()`
- `agent_status()`

### `funktionen_benutzer.php`

Funktionen für Benutzer.

Beispiele:

- `benutzer_array()`
- `benutzer_laden()`
- `benutzer_speichern()`
- `benutzer_loeschen()`

### `funktionen_login.php`

Funktionen für Anmeldung und Abmeldung.

### `funktionen_logs.php`

Zentrale Protokollierungsfunktionen.

### `funktionen_mcp.php`

Funktionen für MCP-Server, MCP-Tools und MCP-Aufrufe.

### `funktionen_navigation.php`

Funktionen zum:

- Scannen des `/nav`-Ordners,
- Auslesen der Metadaten,
- Gruppieren nach Hauptbereich,
- Sortieren nach Priorität,
- Prüfen von Zugriffsrechten.

### `funktionen_rechte.php`

Zentrale Rechteprüfung.

Beispiele:

- `recht_pruefen()`
- `rolle_pruefen()`
- `seite_erlaubt()`

### `funktionen_system.php`

Allgemeine Systemfunktionen.

Beispiele:

- Systemstatus,
- Versionsinformationen,
- Laufzeitinformationen,
- Konfigurationswerte.

### `funktionen_validierung.php`

Zentrale Eingabevalidierung und Bereinigung.

## Layoutdateien

### `header.php`

Enthält den Beginn des HTML-Dokuments sowie Kopfbereich und globale Ressourcen.

### `navigation.php`

Erzeugt Haupt- und Unternavigation.

### `meldungen.php`

Gibt Erfolgs-, Fehler-, Warn- und Informationsmeldungen einheitlich aus.

### `footer.php`

Schließt den Seitenaufbau und bindet zentrale JavaScript-Dateien ein.

---

# 9. Ordner `/js`

Enthält ausschließlich JavaScript.

```text
/js/
├── app.js
├── navigation.js
├── formulare.js
├── tabellen.js
├── ajax.js
└── dashboard.js
```

## Aufteilung

### `app.js`

Globale Initialisierung der Anwendung.

### `navigation.js`

Interaktion mit Hauptnavigation, Untermenüs und mobiler Navigation.

### `formulare.js`

Formularvalidierung und wiederverwendbare Formularfunktionen.

### `tabellen.js`

Sortierung, Filterung, Suche und Pagination.

### `ajax.js`

Zentrale Funktionen für AJAX- beziehungsweise Fetch-Aufrufe.

### `dashboard.js`

Dynamische Dashboard-Aktualisierungen.

Regeln:

- keine Zugangsdaten oder geheimen Werte in JavaScript,
- keine Datenbankzugriffe,
- keine Geschäftslogik, die zwingend serverseitig geprüft werden muss,
- Serverantworten nie ungeprüft vertrauen.

---

# 10. Ordner `/logs`

Enthält dateibasierte technische Logs.

```text
/logs/
├── .htaccess
├── application.log
├── error.log
├── api.log
├── mcp.log
└── security.log
```

## Logdateien

### `application.log`

Allgemeine Anwendungsereignisse.

### `error.log`

Technische Fehler und Exceptions.

### `api.log`

API-Zugriffe, Fehler und Antwortstatus.

### `mcp.log`

MCP-Aufrufe und MCP-Fehler.

### `security.log`

Sicherheitsrelevante Ereignisse:

- fehlgeschlagene Logins,
- Rechteverletzungen,
- gesperrte Zugriffe,
- verdächtige Anfragen.

Der Ordner darf nicht direkt über den Browser erreichbar sein.

Später können wichtige Logs zusätzlich oder vollständig in der Datenbank gespeichert werden.

---

# 11. Ordner `/mcp`

Enthält die technischen Einstiegspunkte für MCP.

```text
/mcp/
├── index.php
├── server.php
├── tools.php
└── resources.php
```

## Aufgaben

### `index.php`

Zentraler Einstiegspunkt des MCP-Bereichs.

### `server.php`

Serverlogik und Verarbeitung eingehender MCP-Anfragen.

### `tools.php`

Definition und Bereitstellung von MCP-Tools.

### `resources.php`

Definition und Bereitstellung von MCP-Ressourcen.

Regeln:

- MCP-Dateien verwenden zentrale Funktionen aus `/inc`.
- Keine doppelte Fachlogik gegenüber API oder Weboberfläche.
- Jeder MCP-Aufruf wird authentifiziert, validiert und protokolliert.
- MCP ist eine Schnittstelle zur vorhandenen Anwendungslogik und keine zweite Anwendung.

---

# 12. Ordner `/nav`

Der Ordner enthält die eigentlichen Inhaltsseiten und steuert gleichzeitig die Navigation.

```text
/nav/
├── main_home.php
├── work_testbereich.php
├── control_logfile.php
└── settings_verwaltung.php
```

## Dateinamensschema

```text
hauptbereich_unterbereich.php
```

Beispiele:

```text
main_home.php
work_testbereich.php
control_logfile.php
settings_verwaltung.php
```

## Hauptbereiche

- `main`
- `work`
- `control`
- `settings`

## Metadaten

Jede Navigationsdatei enthält am Anfang definierte Metadaten.

Beispiel:

```php
<?php
/*
ICON: house
TITLE: Home
PRIO: 10
BEREICH: main
RECHT: dashboard_anzeigen
*/
```

Die Navigation wird automatisch aus diesen Angaben erzeugt.

## Regeln für Navigationsdateien

Navigationsdateien dürfen:

- Inhalte ausgeben,
- zentrale Funktionen aufrufen,
- Formulare verarbeiten,
- Berechtigungen prüfen.

Navigationsdateien dürfen nicht:

- eigene Datenbankverbindungen aufbauen,
- API-Zugriffe direkt duplizieren,
- umfangreiche wiederverwendbare Funktionen definieren,
- globale Konfigurationen überschreiben.

Neue Datei im `/nav`-Ordner bedeutet grundsätzlich neuer Menüpunkt.

---

# 13. Ordner `/sql`

Enthält sämtliche SQL-Definitionen und Datenbankänderungen.

```text
/sql/
├── schema.sql
├── basisdaten.sql
├── testdaten.sql
└── migrations/
    └── README.md
```

## `schema.sql`

Vollständiges initiales Datenbankschema.

## `basisdaten.sql`

Notwendige Startdaten:

- Rollen,
- Rechte,
- Grundeinstellungen,
- Standardwerte.

## `testdaten.sql`

Optionale Daten für Entwicklung und Tests.

Diese Datei darf nicht ungeprüft auf Produktivsystemen ausgeführt werden.

## `/sql/migrations`

Enthält spätere Änderungen am Datenbankschema.

Vorgeschlagenes Dateinamensschema:

```text
YYYYMMDD_HHMM_beschreibung.sql
```

Beispiel:

```text
20260716_1200_benutzer_status_ergaenzen.sql
```

---

# 14. Ordner `/uploads`

Enthält alle durch Benutzer oder Prozesse hochgeladenen Dateien.

```text
/uploads/
├── .htaccess
├── benutzer/
├── agenten/
├── dokumente/
└── temporaer/
```

## Regeln

- Uploads werden niemals ungeprüft ausführbar gespeichert.
- PHP-Ausführung im Uploadordner wird vollständig blockiert.
- Dateityp, MIME-Type und Dateigröße werden geprüft.
- Dateinamen werden serverseitig neu erzeugt.
- Temporäre Dateien werden regelmäßig gelöscht.
- Dateirechte werden restriktiv gesetzt.

## Unterordner

### `/uploads/benutzer`

Benutzerbezogene Dateien und Profilbilder.

### `/uploads/agenten`

Agentenspezifische Dateien.

### `/uploads/dokumente`

Allgemeine Dokumente.

### `/uploads/temporaer`

Kurzzeitig benötigte Dateien.

---

# 15. Ordner `/vendor`

Enthält externe PHP-Abhängigkeiten, bevorzugt über Composer.

```text
/vendor/
└── ...
```

Regeln:

- Dateien in `/vendor` niemals manuell verändern.
- Eigene Funktionen gehören nicht in `/vendor`.
- Abhängigkeiten werden versioniert über `composer.json` verwaltet.
- Der Ordner kann bei Bedarf neu erzeugt werden.

---

# 16. Ordner `/docs`

Enthält die komplette Entwicklungsdokumentation für Mensch und KI-Agent.

```text
/docs/
├── 01-grundentwicklung.md
├── 02-datenbankschema.md
├── 03-coding-rules.md
├── 04-verzeichnisstruktur.md
├── 05-navigation.md
├── 06-api-konzept.md
├── 07-mcp-konzept.md
└── 08-sicherheitskonzept.md
```

Diese Dateien bilden gemeinsam das Entwicklungskonzept.

Der KI-Agent muss vor Änderungen die jeweils relevanten Dokumente lesen und beachten.

---

# 17. Trennung der Verantwortlichkeiten

## HTML

Verantwortlich für:

- semantische Struktur,
- Inhalte,
- Formulare,
- Tabellen,
- UI-Komponenten.

## PHP

Verantwortlich für:

- serverseitige Verarbeitung,
- Sessions,
- Rechte,
- Datenbankzugriffe,
- API,
- MCP,
- Validierung.

## CSS

Verantwortlich für:

- Gestaltung,
- Layout,
- responsive Darstellung,
- Zustände und Komponenten.

## JavaScript

Verantwortlich für:

- Interaktion,
- dynamisches Nachladen,
- Benutzerkomfort,
- Client-seitige Ergänzungen.

Die serverseitige Prüfung darf niemals durch JavaScript ersetzt werden.

---

# 18. Technischer Ladeablauf

```text
Browser
  ↓
Apache
  ↓
index.php
  ↓
inc/config.php
  ↓
inc/datenbank.php
  ↓
inc/session.php
  ↓
inc/funktionen.php
  ↓
inc/header.php
  ↓
inc/navigation.php
  ↓
nav/<angeforderte-seite>.php
  ↓
inc/footer.php
```

Externe oder dynamische Datenflüsse:

```text
Navigationsseite
  ↓
zentrale Funktion aus /inc
  ↓
Datenbank / API / MCP
  ↓
zentrale Funktion
  ↓
Navigationsseite
```

Direktzugriffe aus Navigationsseiten auf Datenbank, API oder MCP sind nicht vorgesehen.

---

# 19. Verbindliche Strukturregeln

1. Jeder Ordner besitzt genau eine Hauptaufgabe.
2. Wiederverwendbare PHP-Logik gehört nach `/inc`.
3. Inhaltsseiten gehören nach `/nav`.
4. API-Endpunkte gehören nach `/api`.
5. MCP-Endpunkte gehören nach `/mcp`.
6. Datenbankdefinitionen gehören nach `/sql`.
7. Statische Bilder gehören nach `/img`.
8. Benutzerdateien gehören nach `/uploads`.
9. CSS gehört ausschließlich nach `/css`.
10. JavaScript gehört ausschließlich nach `/js`.
11. Externe Abhängigkeiten gehören nach `/vendor`.
12. Projektdokumentation gehört nach `/docs`.
13. Keine eigene Datenbankverbindung außerhalb von `/inc/datenbank.php`.
14. Keine doppelte Funktionalität in mehreren Dateien.
15. Keine neue Datei ohne klar definierte Verantwortung.
16. Keine PHP-Ausführung in `/uploads` oder `/logs`.
17. Interne Konfigurations- und Logdateien dürfen nicht öffentlich erreichbar sein.
18. Neue Funktionsbereiche werden als `funktionen_<bereich>.php` angelegt.
19. Neue Navigationsseiten folgen dem Schema `<hauptbereich>_<unterbereich>.php`.
20. Architektur wird vor Modulentwicklung erweitert, nicht währenddessen improvisiert.

---

# 20. Berechtigungen auf Dateisystemebene

Vorgesehener Besitzer:

```text
www-data:www-data
```

Vorgesehene Grundrechte:

```text
Ordner: 755
Dateien: 755
```

Für sensible Dateien soll später geprüft werden, ob restriktivere Rechte wie `640`, `644` oder `750` technisch sinnvoller sind.

Besonders schützenswert:

```text
/inc/config.php
/logs/
/uploads/
/sql/
/docs/
```

Der OpenClaw-Agent arbeitet über den eingebundenen Projektpfad direkt auf derselben Projektkopie.

Es existiert keine separate lokale Entwicklungskopie.

---

# 21. Erweiterung der Struktur

Spätere Module können ergänzt werden, ohne die Grundstruktur zu verändern.

Mögliche Erweiterungen:

```text
/inc/funktionen_modelle.php
/inc/funktionen_jobs.php
/inc/funktionen_workflows.php
/inc/funktionen_prompts.php

/nav/work_agenten.php
/nav/work_prompts.php
/nav/control_jobs.php
/nav/control_systemstatus.php
/nav/settings_modelle.php

/api/modelle.php
/api/jobs.php
/api/workflows.php
```

Die zentrale Struktur bleibt dabei unverändert.

---

# 22. Zusammenfassung

Die Verzeichnisstruktur trennt:

- Einstiegspunkte,
- zentrale PHP-Logik,
- Inhaltsseiten,
- API,
- MCP,
- Datenbankdefinitionen,
- Darstellung,
- Benutzerdateien,
- Logs,
- externe Bibliotheken,
- Entwicklungsdokumentation.

Damit entsteht ein belastbares Grundgerüst für:

- Login,
- Benutzerverwaltung,
- Rollen und Rechte,
- Logfile,
- Dashboard,
- Agenten,
- Modelle,
- API,
- MCP,
- spätere Workflow- und Automatisierungsfunktionen.
