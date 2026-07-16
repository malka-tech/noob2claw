# 05_Seitenaufbau.md

# Seitenaufbau

Version: 1.0

Dieses Dokument definiert den einheitlichen Aufbau aller Seiten der Anwendung.

Jede Seite muss diesem Aufbau folgen.

Dadurch entsteht eine konsistente Architektur, eine einheitliche Darstellung und eine einfache Wartbarkeit.

---

# Ziel

Alle Seiten sollen:

- identisch aufgebaut sein
- denselben Ablauf besitzen
- dieselben Komponenten verwenden
- leicht erweiterbar sein
- keine doppelte Logik enthalten

---

# Grundprinzip

Es existiert nur ein zentraler Einstiegspunkt.

```text
index.php
```

Alle Seiten werden ausschließlich über die `index.php` geladen.

Es gibt keine eigenständigen PHP-Seiten außerhalb dieses Prinzips.

---

# Seitenaufbau

Der grundsätzliche Ablauf lautet:

```text
Browser
        │
        ▼
index.php
        │
        ▼
config.php
        │
        ▼
datenbank.php
        │
        ▼
session.php
        │
        ▼
funktionen.php
        │
        ▼
Rechteprüfung
        │
        ▼
header.php
        │
        ▼
navigation.php
        │
        ▼
nav/<seite>.php
        │
        ▼
footer.php
```

---

# index.php

Die index.php ist ausschließlich für den Seitenaufbau verantwortlich.

Sie darf keine Geschäftslogik enthalten.

Aufgaben:

- Konfiguration laden
- Datenbank verbinden
- Session starten
- Funktionen laden
- Login prüfen
- Rechte prüfen
- Navigation erzeugen
- gewünschte Seite laden
- Footer laden

---

# config.php

Die Konfiguration wird immer zuerst geladen.

Sie enthält ausschließlich:

- Systemeinstellungen
- Datenbankparameter
- Konstanten
- Pfade
- Debugmodus
- Version

Keine Logik.

---

# datenbank.php

Stellt ausschließlich die Datenbankverbindung bereit.

Keine SQL-Abfragen.

Keine Fachlogik.

---

# session.php

Verantwortlich für:

- Session starten
- Loginstatus
- Sessionprüfung
- Timeout
- Session-Regeneration

---

# funktionen.php

Lädt automatisch sämtliche

```text
funktionen_*.php
```

Dadurch stehen im gesamten Projekt alle zentralen Funktionen zur Verfügung.

---

# Rechteprüfung

Vor jeder Seite wird geprüft:

- Benutzer angemeldet
- Benutzer aktiv
- Rolle vorhanden
- Berechtigung vorhanden

Ist dies nicht erfüllt:

```text
Fehlermeldung

oder

Login
```

---

# header.php

Der Header enthält:

- HTML-Grundgerüst
- Meta-Tags
- CSS-Dateien
- JavaScript-Dateien
- Kopfbereich
- Logo

Keine Geschäftslogik.

---

# navigation.php

Erzeugt automatisch:

- Hauptnavigation
- Untermenüs
- aktive Seite
- Rechtefilter

Neue Navigationspunkte entstehen ausschließlich durch neue Dateien im nav-Ordner.

---

# Inhaltsbereich

Der eigentliche Inhalt befindet sich ausschließlich im Ordner

```text
/nav/
```

Beispiel:

```text
main_dashboard.php

work_agenten.php

settings_system.php
```

Diese Dateien enthalten ausschließlich den jeweiligen Seiteninhalt.

---

# footer.php

Der Footer beendet:

- HTML
- JavaScript
- Dialoge
- globale Komponenten

Keine Fachlogik.

---

# Verantwortlichkeiten

## index.php

Nur Seitenaufbau

---

## Header

Nur Layout

---

## Navigation

Nur Navigation

---

## Inhaltsseite

Nur Fachinhalt

---

## Footer

Nur Abschluss

---

# Datenfluss

Der Datenfluss erfolgt immer gleich.

```text
Browser

↓

index.php

↓

Inhaltsseite

↓

Funktion

↓

Datenbank / API / MCP

↓

Funktion

↓

Inhaltsseite

↓

Browser
```

Die Inhaltsseite greift niemals direkt auf die Datenbank zu.

---

# Aufbau einer Inhaltsseite

Eine Inhaltsseite besitzt grundsätzlich folgenden Ablauf:

```php
Rechte prüfen

↓

Daten laden

↓

Formulare verarbeiten

↓

HTML ausgeben

↓

JavaScript initialisieren
```

---

# Formulare

Formulare befinden sich immer innerhalb der jeweiligen Inhaltsseite.

Die Verarbeitung erfolgt über zentrale Funktionen.

Nicht direkt über SQL.

---

# Datenbankzugriffe

Inhaltsseiten dürfen niemals enthalten:

```php
mysqli_query()

PDO->query()

INSERT

UPDATE

DELETE
```

Diese gehören ausschließlich in zentrale Funktionen.

---

# API-Aufrufe

Auch API-Aufrufe erfolgen ausschließlich über zentrale Funktionen.

Nicht:

```php
curl

fetch

direkte Requests
```

auf beliebigen Seiten.

---

# Wiederverwendbare Komponenten

Folgende Komponenten sollen projektweit wiederverwendet werden:

- Tabellen
- Buttons
- Karten
- Formulare
- Hinweise
- Fehlermeldungen
- Dialoge
- Statusanzeigen

Es existiert jeweils nur eine Standardimplementierung.

---

# Seitenlayout

Jede Seite besitzt denselben Aufbau.

```text
+--------------------------------------+
| Header                               |
+--------------------------------------+
| Navigation                           |
+--------------------------------------+
| Seitenüberschrift                    |
|--------------------------------------|
| Inhalt                               |
|                                      |
|                                      |
|                                      |
+--------------------------------------+
| Footer                               |
+--------------------------------------+
```

---

# Mobile Darstellung

Der Seitenaufbau muss vollständig responsive sein.

Desktop

↓

Tablet

↓

Smartphone

dürfen keine unterschiedlichen Funktionen besitzen.

Lediglich die Darstellung ändert sich.

---

# Fehlerdarstellung

Fehler werden immer einheitlich dargestellt.

Beispiele:

- Erfolg
- Hinweis
- Warnung
- Fehler

Es existiert ein gemeinsames Meldungssystem.

---

# Navigation

Navigation wird niemals manuell programmiert.

Sie entsteht automatisch aus den Dateien des

```text
/nav
```

Ordners.

---

# Erweiterbarkeit

Neue Seiten entstehen ausschließlich durch:

1.

Neue Datei im

```text
/nav
```

2.

Metadaten ergänzen

3.

Funktion schreiben

Fertig.

Es müssen keine Menüs angepasst werden.

---

# Verboten

Nicht erlaubt:

- SQL im HTML
- Geschäftslogik im Header
- Datenbankzugriffe im Footer
- HTML in Funktionen
- direkte API-Aufrufe
- mehrere Einstiegspunkte
- unterschiedliche Seitenlayouts

---

# Ziel

Jede Seite der Anwendung besitzt denselben technischen Aufbau.

Dadurch entstehen:

- einheitlicher Code
- einfache Erweiterbarkeit
- klare Verantwortlichkeiten
- weniger Fehler
- optimale Unterstützung für KI-gestützte Entwicklung