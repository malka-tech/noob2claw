# 10_Dashboard_und_Module.md

# Dashboard und Module

Version: 1.0

Dieses Dokument definiert den grundsätzlichen Aufbau sämtlicher Module der Anwendung.

Ein Modul ist ein fachlich abgeschlossener Bereich der Anwendung.

Beispiele:

- Agenten
- Modelle
- Prompts
- Workflows
- Benutzer
- Logs
- Systemeinstellungen

Alle Module folgen derselben Struktur.

---

# Ziel

Jedes Modul soll

- identisch aufgebaut sein
- automatisch in die Navigation integriert werden
- dieselbe Oberfläche verwenden
- dieselben Komponenten verwenden
- leicht erweitert werden können

---

# Grundprinzip

Ein Modul besteht immer aus vier Ebenen.

```text
Navigation

↓

Oberfläche

↓

Business-Logik

↓

Datenbank
```

---

# Aufbau eines Moduls

Ein vollständiges Modul besteht mindestens aus

```text
nav/

↓

Inhaltsseite

↓

Funktionen

↓

Datenbank
```

Beispiel

```text
work_agenten.php

↓

funktionen_agenten.php

↓

Tabellen agenten
```

---

# Navigation

Jedes Modul besitzt mindestens einen Navigationseintrag.

Die Navigation entsteht automatisch.

---

# Seitenaufbau

Jedes Modul besitzt denselben Aufbau.

```text
Überschrift

↓

Toolbar

↓

Filter

↓

Inhalt

↓

Dialoge
```

---

# Überschrift

Jede Seite besitzt

- Titel
- Beschreibung
- Icon

---

# Toolbar

Die Toolbar enthält Aktionen.

Beispiele

```text
Neu

Bearbeiten

Löschen

Aktualisieren

Import

Export
```

---

# Filter

Filter werden oberhalb der Tabelle angezeigt.

Beispiele

- Suche
- Status
- Kategorie
- Benutzer
- Zeitraum

---

# Hauptbereich

Der Hauptbereich besteht aus einer oder mehreren Komponenten.

Beispiele

- Tabelle
- Dashboard
- Karten
- Diagramme
- Formulare

---

# Tabellen

Tabellen besitzen grundsätzlich

- Sortierung
- Suche
- Filter
- Pagination
- Mehrfachauswahl

---

# Formulare

Alle Formulare besitzen denselben Aufbau.

```text
Titel

↓

Beschreibung

↓

Eingabefelder

↓

Speichern

↓

Abbrechen
```

---

# Buttons

Es werden projektweit dieselben Button-Typen verwendet.

Beispiele

- Primär
- Sekundär
- Erfolg
- Warnung
- Gefahr

---

# Dialoge

Dialogfenster werden zentral verwendet.

Beispiele

- Löschen
- Bearbeiten
- Informationen
- Einstellungen

---

# Dashboard

Das Dashboard besteht ausschließlich aus wiederverwendbaren Widgets.

Beispiele

```text
Karte

Statistik

Diagramm

Aktivität

Status

Log

Liste
```

---

# Widgets

Jedes Widget besitzt

- Titel
- Icon
- Inhalt
- Aktualisierung
- Berechtigung

Widgets können beliebig kombiniert werden.

---

# Karten

Informationskarten besitzen immer denselben Aufbau.

```text
Icon

↓

Titel

↓

Wert

↓

Zusatzinformation
```

---

# Listen

Listen verwenden dieselben Komponenten wie Tabellen.

---

# Icons

Icons werden ausschließlich zentral definiert.

Es existiert projektweit genau ein Icon-Set.

---

# Farben

Statusfarben sind projektweit identisch.

Beispiele

Grün

Aktiv

Blau

Information

Gelb

Warnung

Rot

Fehler

Grau

Inaktiv

---

# Module erweitern

Neue Module entstehen immer nach demselben Ablauf.

```text
Navigation

↓

Seite

↓

Funktionen

↓

Tabellen

↓

Fertig
```

---

# Dashboard erweitern

Neue Widgets können jederzeit ergänzt werden.

Bestehende Widgets dürfen wiederverwendet werden.

---

# Verboten

Nicht erlaubt sind

- unterschiedliche Seitenlayouts
- unterschiedliche Tabellen
- unterschiedliche Formulare
- unterschiedliche Button-Designs
- individuelle Dialoge
- eigene Dashboard-Strukturen

---

# Ziel

Alle Module besitzen denselben Aufbau.

Dadurch entsteht eine einheitliche Benutzeroberfläche, die sich sowohl für Entwickler als auch für KI-Agenten einfach erweitern lässt.

Neue Module können ohne Änderungen an der Grundarchitektur ergänzt werden.