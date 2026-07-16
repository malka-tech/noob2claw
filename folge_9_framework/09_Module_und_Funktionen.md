# 09_Module_und_Funktionen.md

# Module und Funktionen

Version: 2.0

Dieses Dokument definiert den Aufbau sämtlicher PHP-Module und Funktionen der Anwendung.

Die komplette Business-Logik befindet sich ausschließlich im Ordner

```text
/inc/
```

Alle Module werden automatisch geladen und stehen der Weboberfläche, der REST-API sowie dem MCP-Server gleichermaßen zur Verfügung.

Es existiert immer genau eine Implementierung einer Funktion.

---

# Ziel

Die Business-Logik soll

- modular aufgebaut sein
- leicht verständlich sein
- beliebig erweiterbar sein
- wiederverwendbar sein
- möglichst kleine Dateien besitzen
- eindeutig einer Datenbanktabelle oder einem Fachobjekt zugeordnet sein

---

# Grundprinzip

Jede Datenbanktabelle besitzt genau eine Funktionsdatei.

Beispiel

```text
Tabelle

↓

Funktionsdatei

↓

Business-Logik
```

Dadurch findet jeder Entwickler und jede KI sofort die richtige Datei.

---

# Aufbau des INC-Ordners

```text
/inc/

config.php
datenbank.php
session.php

funktionen.php

header.php
navigation.php
footer.php
meldungen.php
```

Die Datei

```text
funktionen.php
```

lädt automatisch sämtliche Dateien nach folgendem Muster

```text
funktionen_*.php
```

Es muss niemals eine neue Datei manuell eingebunden werden.

---

# Grundmodule

Folgende Module bilden die technische Basis.

```text
funktionen_cache.php
funktionen_config.php
funktionen_crypto.php
funktionen_dateien.php
funktionen_datenbank.php
funktionen_debug.php
funktionen_json.php
funktionen_logs.php
funktionen_navigation.php
funktionen_rechte.php
funktionen_session.php
funktionen_system.php
funktionen_validierung.php
```

Diese Dateien enthalten ausschließlich allgemeine Systemfunktionen.

---

# Fachmodule

Jede Datenbanktabelle besitzt eine eigene Funktionsdatei.

## Benutzer

```text
funktionen_benutzer.php
```

Verarbeitet ausschließlich die Tabelle

```text
benutzer
```

---

## Benutzergruppen

```text
funktionen_benutzer_gruppen.php
```

Verarbeitet ausschließlich

```text
benutzer_gruppen
```

---

## Benutzergruppen-Zuordnung

```text
funktionen_benutzer_gruppen_zuordnung.php
```

---

## Rechte

```text
funktionen_rechte.php
```

---

## Benutzergruppen-Rechte

```text
funktionen_benutzer_gruppen_rechte.php
```

---

## Benutzer-Log

```text
funktionen_benutzer_log.php
```

---

## Benutzer-Logins

```text
funktionen_benutzer_logins.php
```

---

## Sessions

```text
funktionen_sessions.php
```

---

## API-Benutzer

```text
funktionen_api_benutzer.php
```

---

## API-Log

```text
funktionen_api_log.php
```

---

## MCP-Benutzer

```text
funktionen_mcp_benutzer.php
```

---

## MCP-Log

```text
funktionen_mcp_log.php
```

---

## Agenten

```text
funktionen_agents.php
```

---

## Agenten-Log

```text
funktionen_agents_log.php
```

---

## Einstellungen

```text
funktionen_einstellungen.php
```

---

## Datenbankfehler

```text
funktionen_db_fehler_log.php
```

---

## Systemlog

```text
funktionen_system_log.php
```

---

# Aufbau einer Funktionsdatei

Jede Datei besitzt denselben Aufbau.

```php
Dateikopf

↓

Hilfsfunktionen

↓

CRUD-Funktionen

↓

Spezialfunktionen

↓

Interne Funktionen
```

---

# CRUD-Funktionen

Jede Tabelle besitzt mindestens folgende Funktionen.

```php
xxx_array()

xxx_laden()

xxx_speichern()

xxx_loeschen()

xxx_existiert()
```

Beispiel

```php
benutzer_array()

benutzer_laden()

benutzer_speichern()

benutzer_loeschen()

benutzer_existiert()
```

---

# Weitere Funktionen

Zusätzlich können fachliche Funktionen ergänzt werden.

Beispiele

```php
benutzer_anmelden()

benutzer_abmelden()

benutzer_passwort_setzen()

benutzer_sperren()

benutzer_freigeben()
```

---

# Namenskonvention

Alle Funktionen beginnen mit dem Namen des Fachobjektes.

Beispiele

```php
agent_laden()

agent_starten()

agent_stoppen()

agent_status()

agent_logs()
```

Nicht

```php
starten()

laden()

stoppen()

status()
```

---

# Keine doppelte Logik

Nicht erlaubt

```text
Web

↓

eigene Funktion

API

↓

eigene Funktion

MCP

↓

eigene Funktion
```

Sondern

```text
Web

↓

API

↓

MCP

↓

gemeinsame Funktion

↓

Datenbank
```

---

# HTML

Funktionsdateien erzeugen niemals HTML.

Nicht

```php
echo "<table>";
```

---

# JSON

Funktionsdateien erzeugen ebenfalls kein JSON.

JSON wird ausschließlich von

```text
api.php
```

erzeugt.

---

# MCP

Funktionsdateien erzeugen keine MCP-Antworten.

Die Umsetzung erfolgt ausschließlich in

```text
mcp.php
```

---

# SQL

SQL befindet sich ausschließlich innerhalb der jeweiligen Funktionsdatei.

Keine SQL-Abfragen in

- Navigation
- index.php
- api.php
- mcp.php
- HTML-Dateien

---

# Logging

Geschäftskritische Funktionen schreiben automatisch Logeinträge.

Beispiele

```text
Benutzer angelegt

Benutzer geändert

Agent gestartet

Agent gestoppt

Einstellung geändert
```

---

# Rechteprüfung

Geschäftskritische Funktionen prüfen ihre Berechtigungen zusätzlich selbst.

Beispiel

```php
agent_loeschen()
```

prüft intern

```php
recht_pruefen("agent_loeschen");
```

Dadurch bleibt die Sicherheit unabhängig vom Aufrufer erhalten.

---

# Erweiterung

Neue Tabellen werden immer gleich integriert.

```text
Neue Tabelle

↓

Neue Funktionsdatei

↓

Automatischer Loader

↓

Fertig
```

Es müssen keine weiteren Dateien angepasst werden.

---

# Verboten

Nicht erlaubt sind

- HTML innerhalb der Funktionen
- SQL außerhalb der Funktionen
- JSON innerhalb der Funktionen
- doppelte Business-Logik
- Funktionen ohne eindeutigen Fachbereich
- mehrere Dateien für dieselbe Tabelle

---

# Ziel

Jede Datenbanktabelle besitzt genau eine Funktionsdatei.

Jede Funktion existiert genau einmal.

Alle Bereiche der Anwendung – Weboberfläche, REST-API und MCP – greifen auf dieselbe Business-Logik zu.

Dadurch entsteht eine klare, skalierbare und KI-freundliche Architektur, die auch bei mehreren hundert Funktionen übersichtlich bleibt.