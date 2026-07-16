# 06_Navigation.md

# Navigation

Version: 1.0

Dieses Dokument beschreibt den vollständigen Aufbau der Navigation des Webinterfaces.

Die Navigation wird niemals manuell programmiert, sondern vollständig automatisch aus den vorhandenen Dateien erzeugt.

Dadurch muss beim Erweitern der Anwendung keine Navigation angepasst werden.

---

# Ziel

Die Navigation soll:

- vollständig automatisch erzeugt werden
- beliebig erweiterbar sein
- Rollen und Rechte berücksichtigen
- einheitlich aussehen
- keine doppelte Konfiguration benötigen

---

# Grundprinzip

Jede Datei im Ordner

```text
/nav
```

stellt genau einen Menüpunkt dar.

Neue Datei = neuer Menüpunkt.

Es werden keine Menüeinträge manuell angelegt.

---

# Aufbau

```text
/nav

main_dashboard.php

main_startseite.php

work_agenten.php

work_modelle.php

control_logs.php

control_jobs.php

settings_benutzer.php

settings_system.php
```

---

# Dateinamensschema

Jede Datei besitzt folgendes Schema

```text
hauptbereich_unterbereich.php
```

Beispiele

```text
main_dashboard.php

work_agenten.php

settings_system.php
```

---

# Hauptbereiche

Folgende Hauptbereiche existieren

```text
main

work

control

settings
```

Diese bilden die Hauptnavigation.

---

# Unterbereiche

Alles hinter dem Unterstrich bildet den eigentlichen Menüpunkt.

Beispiel

```text
work_agenten.php
```

ergibt

```text
Work

↓

Agenten
```

---

# Metadaten

Jede Datei beginnt mit einem Kommentarblock.

Beispiel

```php
/*
ICON: robot
TITLE: Agenten
PRIO: 20
BEREICH: work
RECHT: agenten_anzeigen
*/
```

---

# Pflichtfelder

Jede Navigationsdatei besitzt mindestens

```text
ICON

TITLE

PRIO

BEREICH

RECHT
```

---

# ICON

Bestimmt das Symbol.

Beispiel

```text
house

robot

users

database

settings

terminal

cpu

file-text
```

---

# TITLE

Angezeigter Menüname.

Beispiel

```text
Dashboard

Agenten

System

Logs

Benutzer
```

---

# PRIO

Legt die Reihenfolge fest.

Beispiel

```text
10

20

30

40
```

Kleinere Zahl = weiter oben.

---

# BEREICH

Legt den Hauptbereich fest.

Beispiel

```text
main

work

control

settings
```

---

# RECHT

Legt fest, welche Benutzer den Menüpunkt sehen dürfen.

Beispiel

```text
dashboard

agenten

system

logs
```

---

# Navigation erzeugen

Der Ablauf

```text
Ordner lesen

↓

Dateien finden

↓

Metadaten lesen

↓

Nach Bereich gruppieren

↓

Nach Priorität sortieren

↓

Rechte prüfen

↓

Navigation erzeugen
```

---

# Rechte

Vor jedem Menüpunkt wird geprüft

```text
Hat Benutzer dieses Recht?
```

Ja

↓

anzeigen

Nein

↓

nicht anzeigen

---

# Aktiver Menüpunkt

Die aktuelle Seite wird automatisch markiert.

Es erfolgt keine manuelle Definition.

---

# Neue Seite

Neue Seiten entstehen ausschließlich durch

1.

Datei anlegen

↓

2.

Metadaten eintragen

↓

3.

Inhalt entwickeln

↓

fertig

---

# Löschen

Eine Seite verschwindet automatisch aus der Navigation sobald die Datei entfernt wird.

---

# Sortierung

Es wird ausschließlich nach

```text
PRIO
```

sortiert.

Keine alphabetische Sortierung.

---

# Untermenüs

Untermenüs entstehen automatisch innerhalb des jeweiligen Hauptbereichs.

Beispiel

```text
Work

↓

Agenten

Modelle

Prompts

Workflows
```

---

# Dashboard

Die Startseite wird über die niedrigste Priorität innerhalb von

```text
main
```

bestimmt.

---

# Erweiterbarkeit

Neue Hauptbereiche können jederzeit ergänzt werden.

Beispiel

```text
analyse

ki

entwicklung
```

Die Navigation funktioniert weiterhin automatisch.

---

# Verboten

Nicht erlaubt

- Menü manuell programmieren
- Menü doppelt definieren
- Rechte im HTML prüfen
- Reihenfolge hart codieren
- Icons mehrfach definieren

---

# Ziel

Die Navigation soll vollständig datengetrieben arbeiten.

Der Entwickler erstellt lediglich neue Dateien.

Alles Weitere übernimmt automatisch das System.