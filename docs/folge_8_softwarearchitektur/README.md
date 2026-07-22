# <p align="center"><img src="/images/logo.png" width="420"></p>

# Noob2Claw Framework

> Entwickle mit KI eine vollständige Weboberfläche für OpenClaw – Schritt für Schritt, modular und professionell.

Dieses Repository begleitet die **Noob2Claw-Videoserie** und bildet die Grundlage für die komplette Weboberfläche, die wir über die kommenden Folgen gemeinsam entwickeln.

# 🌐 Noob2Claw-Webseite

Weitere Informationen, Anleitungen und Neuigkeiten zum Projekt findet ihr auf [noob2claw.de](https://noob2claw.de/).

📺 **Video zur Folge 08**

https://www.youtube.com/watch?v=ZckcAkLHFVw

---

# Ziel des Projekts

Ziel ist der Aufbau einer vollständig modularen Weboberfläche für OpenClaw.

Dabei soll eine Architektur entstehen, die

- einfach erweitert werden kann
- sauber strukturiert ist
- mit KI entwickelt werden kann
- mehrere Entwickler unterstützt
- langfristig wartbar bleibt

Die eigentliche Anwendung entsteht nahezu vollständig durch **Vibe Coding**.

Das bedeutet jedoch nicht, dass die KI einfach blind Code erzeugt.

Der wichtigste Teil der Entwicklung ist die Architektur.

---

# Was bedeutet Vibe Coding?

Viele verstehen unter Vibe Coding lediglich:

> "Ich schreibe einen Prompt und die KI programmiert."

Genau das funktioniert bei größeren Projekten **nicht**.

In dieser Serie wird deshalb ein anderer Ansatz verfolgt.

Der Entwickler definiert:

- Architektur
- Projektstruktur
- Regeln
- Coding Standards
- Verantwortlichkeiten
- Workflows

Erst anschließend beginnt die KI mit der eigentlichen Entwicklung.

Dadurch entstehen deutlich stabilere Projekte.

---

# Projektarchitektur

Die Entwicklungsumgebung besteht aus zwei getrennten virtuellen Maschinen.

## Ubuntu

Hier läuft:

- OpenClaw
- Claude
- GPT
- Agenten
- Entwicklung

Ubuntu dient ausschließlich als Entwicklungsserver.

---

## Debian

Hier läuft der eigentliche Webserver.

Installiert sind:

- Apache
- PHP
- MariaDB

Projektverzeichnis:

```

/var/www/noobclaw

```

---

# Warum zwei Systeme?

Dadurch werden Entwicklung und Produktivsystem sauber getrennt.

Der Entwickler arbeitet ausschließlich auf Ubuntu.

Apache arbeitet ausschließlich auf Debian.

---

# Gemeinsames Projekt

Anstatt Dateien ständig zu kopieren, existiert lediglich **eine einzige Projektkopie**.

```

Ubuntu
(OpenClaw)

│

SSHFS

│

▼

Debian

/var/www/noobclaw

```

Der Entwickler arbeitet somit direkt auf dem Webserver.

Jede Änderung ist sofort im Browser sichtbar.

Es existieren keine Synchronisationsprobleme.

---

# SSHFS-Mount

Das Projekt wird über SSHFS eingebunden.

Der Benutzer

```

noobclaw

```

arbeitet direkt im Webverzeichnis.

Es gibt

- keine zweite Projektkopie
- kein Git-Sync zwischen Maschinen
- kein Deployment

Der Browser sieht sofort jede Änderung.

---

# Entwicklungsworkflow

Die komplette Entwicklung erfolgt über einen wiederholbaren Workflow.

```

Masterprompt

↓

OpenClaw

↓

PHP Dateien

↓

Apache

↓

Browser

↓

Feedback

↓

Verbesserung

```

Die KI entwickelt.

Der Entwickler bewertet.

Danach beginnt die nächste Iteration.

---

# Projektstruktur

```

api/
css/
fonts/
img/
inc/
js/
logs/
mcp/
nav/
sql/
uploads/
vendor/

```

Jeder Ordner besitzt genau **eine** Aufgabe.

Es gibt keine gemischten Verantwortlichkeiten.

---

# Der INC-Ordner

Der Ordner

```

inc/

```

enthält ausschließlich technische Logik.

Dazu gehören beispielsweise:

- Konfiguration
- Datenbank
- Header
- Footer
- Hilfsfunktionen

---

# Funktionen sauber trennen

Anstatt eine riesige PHP-Datei zu erzeugen, besitzt jedes Thema seine eigene Datei.

Beispiele:

```

funktionen_api.php
funktionen_agents.php
funktionen_benutzer.php
funktionen_login.php
funktionen_logs.php
funktionen_mcp.php
funktionen_navigation.php
funktionen_rechte.php
funktionen_system.php

```

Dadurch bleibt das Projekt auch bei vielen tausend Zeilen Code übersichtlich.

---

# Automatischer Loader

Alle Dateien

```

funktionen_\*.php

```

werden automatisch geladen.

Neue Funktionen müssen dadurch lediglich als neue Datei angelegt werden.

Der zentrale Loader übernimmt den Rest.

---

# Einheitliche Funktionsnamen

Alle Module besitzen einheitliche Einstiegspunkte.

Beispiele:

```

benutzer_array()
agenten_array()
api_array()
rollen_array()
mcp_array()

```

Dadurch weiß die KI jederzeit, wie Daten abgefragt werden.

---

# Automatische Navigation

Die Navigation wird nicht von Hand programmiert.

Neue Datei im Ordner

```

nav/

```

=

neuer Menüpunkt.

Mehr ist nicht notwendig.

---

# Navigation über Metadaten

Jede Datei definiert ihre Eigenschaften selbst.

Zum Beispiel:

- Bereich
- Icon
- Titel
- Priorität

Dadurch entsteht automatisch die Navigation.

---

# Bereiche

Die Oberfläche ist in vier Hauptbereiche aufgeteilt.

## Main

Startseite

---

## Work

Arbeitsbereich

---

## Control

Logs

Kontrolle

Debugging

---

## Settings

Konfiguration

Verwaltung

---

# Seitenaufbau

Jede Seite besitzt denselben Aufbau.

```

index.php

↓

config

↓

funktionen

↓

header

↓

Navigation

↓

Inhalt

↓

footer

```

Dadurch bleibt die komplette Anwendung konsistent.

---

# Datenfluss

```

Browser

↓

Apache

↓

PHP

↓

Funktionen

↓

Datenbank

↓

API

↓

MCP

```

Direkte Datenbankzugriffe sollen möglichst vermieden werden.

Alle Zugriffe erfolgen zentral.

---

# API und MCP

Die komplette Kommunikation mit

- APIs
- MCP Servern
- Agenten

erfolgt über zentrale Funktionen.

Dadurch können Implementierungen später problemlos geändert werden.

---

# Warum diese Architektur?

Diese Architektur ermöglicht:

- einfache Erweiterungen
- wiederverwendbaren Code
- saubere Verantwortlichkeiten
- weniger Fehler
- bessere Zusammenarbeit mit KI
- einfacheres Refactoring

---

# Entwicklungsregeln

Während der Entwicklung gelten einige Grundregeln.

- Architektur vor Implementierung
- Kleine Schritte
- Keine doppelten Funktionen
- Keine Logik mehrfach schreiben
- Zentrale Funktionen verwenden
- Module statt Monolithen
- Erst testen, dann erweitern

---

# Die ersten Module

In den kommenden Folgen entstehen unter anderem:

- Login
- Benutzerverwaltung
- Rollen
- Rechte
- Dashboard
- Logfile
- Agentenverwaltung
- MCP
- API
- Einstellungen

---

# Roadmap

## Folge 08

Projektarchitektur

Grundgerüst

Projektstruktur

---

## Folge 09

Login

---

## Folge 10

Benutzerverwaltung

---

## Folge 11

Agenten

---

Danach folgen weitere Module wie MCP, APIs, Dashboards, Rechteverwaltung und viele weitere Komponenten.

---

# Ziel der gesamten Serie

Am Ende der Videoserie entsteht eine vollständige Weboberfläche für OpenClaw mit

- Benutzerverwaltung
- Rechtekonzept
- Agentenverwaltung
- MCP-Anbindung
- API-Management
- Dashboards
- Logging
- Erweiterbare Module
- Moderner Projektstruktur
- KI-gestützter Entwicklung

---

# Lizenz

Die Lizenzbedingungen befinden sich in

```

99_lizenz.md

```

---

# Noob2Claw

Die Noob2Claw-Serie richtet sich an alle, die lernen möchten,

- moderne KI-Agenten einzusetzen,
- professionelle Softwarearchitekturen aufzubauen,
- Vibe Coding richtig anzuwenden und
- eine vollständige OpenClaw-Plattform selbst zu entwickeln.
