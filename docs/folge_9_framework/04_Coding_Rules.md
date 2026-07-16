# 04_Coding_Rules.md

# Coding Rules

Version: 1.0

Dieses Dokument definiert die verbindlichen Coding-Regeln für das gesamte Projekt.

Diese Regeln gelten für **alle** Dateien, unabhängig davon, ob sie von einem Entwickler oder einer KI erstellt wurden.

Alle zukünftigen Entwicklungen müssen sich an diesen Standards orientieren.

---

# Ziel

Der gesamte Quellcode soll:

- leicht verständlich sein
- leicht wartbar sein
- wiederverwendbar sein
- möglichst wenig Fehler enthalten
- von jeder KI problemlos erweitert werden können

Nicht die kürzeste Lösung gewinnt, sondern die sauberste.

---

# Grundregeln

Vor jeder Änderung muss geprüft werden:

- Existiert bereits eine passende Funktion?
- Existiert bereits eine passende Klasse?
- Existiert bereits ein passendes Modul?
- Existiert bereits eine passende Datenbankfunktion?

Erst wenn dies nicht der Fall ist, darf neuer Code entstehen.

---

# Keine Duplikate

Code darf niemals mehrfach existieren.

Nicht:

```php
function benutzer_laden() {}
```

und später

```php
function lade_benutzer() {}
```

für dieselbe Aufgabe.

Jede Funktion existiert genau einmal.

---

# Eine Aufgabe pro Funktion

Jede Funktion besitzt genau eine Verantwortung.

Schlecht:

```php
login()
```

Diese Funktion:

- prüft Passwort
- lädt Benutzer
- schreibt Logs
- erzeugt Session
- versendet Mail

Gut:

```php
login_pruefen()

benutzer_laden()

session_starten()

login_protokollieren()

mail_senden()
```

---

# Kleine Funktionen

Eine Funktion soll möglichst kurz bleiben.

Empfehlung:

- maximal 30–50 Zeilen
- nur eine Aufgabe
- klarer Name

Große Funktionen werden aufgeteilt.

---

# Sprechende Namen

Variablen, Funktionen und Dateien müssen ihren Zweck eindeutig beschreiben.

Gut:

```php
benutzer_laden()

agenten_array()

rechte_pruefen()

modell_speichern()
```

Schlecht:

```php
lade()

array()

test()

tmp()

abc()
```

---

# Deutsche Bezeichnungen

Die gesamte Fachlogik wird auf Deutsch entwickelt.

Beispiele:

```php
benutzer

rollen

rechte

agenten

modelle
```

Nicht:

```php
users

roles

rights
```

Technische Begriffe wie API, JSON oder HTML bleiben selbstverständlich englisch.

---

# Dateinamen

Dateien werden ausschließlich klein geschrieben.

Wörter werden mit Unterstrichen getrennt.

Beispiele:

```text
funktionen_login.php

funktionen_agenten.php

main_dashboard.php
```

Nicht:

```text
Login.php

AgentFunctions.php

DashboardMain.php
```

---

# Funktionsnamen

Funktionsnamen bestehen aus:

```text
bereich_aktion()
```

Beispiele:

```php
benutzer_laden()

benutzer_speichern()

rechte_pruefen()

rolle_anlegen()

agent_starten()

modell_laden()
```

---

# Variablennamen

Variablen besitzen sprechende Namen.

Gut:

```php
$benutzer

$rolle

$email

$agent

$modell
```

Nicht:

```php
$a

$tmp

$x

$data1
```

---

# Einrückung

Einheitlich:

- 4 Leerzeichen
- keine Tabs

---

# Zeilenlänge

Möglichst unter:

```text
120 Zeichen
```

Bleibt eine Zeile länger, wird sinnvoll umgebrochen.

---

# Kommentare

Nicht jeder Code benötigt Kommentare.

Kommentare erklären:

- warum etwas gemacht wird
- nicht was gemacht wird

Schlecht:

```php
// Schleife starten

foreach (...)
```

Gut:

```php
// Nur aktive Benutzer laden,
// damit gesperrte Benutzer nicht angezeigt werden.
```

---

# Header jeder Datei

Jede Datei beginnt mit einer kurzen Beschreibung.

Beispiel:

```php
/*
----------------------------------------
Datei:

funktionen_agenten.php

Beschreibung:

Zentrale Funktionen zur Verwaltung
von KI-Agenten.
----------------------------------------
*/
```

---

# Rückgabewerte

Funktionen besitzen eindeutige Rückgabewerte.

Nicht:

```php
true

false

1

0

array

string
```

durcheinander.

Sondern:

immer klar definiert.

---

# Fehlerbehandlung

Fehler dürfen niemals ignoriert werden.

Nicht:

```php
try {

}

catch (Exception $e) {

}
```

ohne Inhalt.

Fehler werden:

- geloggt
- verständlich behandelt
- sauber zurückgegeben

---

# Keine Magic Numbers

Nicht:

```php
if ($rolle == 3)
```

Sondern:

```php
if ($rolle == ROLLE_ADMIN)
```

oder

```php
if ($rolle == "Administrator")
```

---

# Keine Hardcodierungen

Nicht:

```php
$mail = "test@test.de";
```

Konfigurationen gehören in:

```text
config.php
```

oder

die Datenbank.

---

# SQL

SQL gehört niemals verteilt in die Anwendung.

Alle Datenbankzugriffe laufen über zentrale Funktionen.

Nicht:

```php
mysqli_query(...)
```

auf beliebigen Seiten.

---

# HTML

HTML enthält:

- Struktur
- Formulare
- Tabellen
- Komponenten

Keine Geschäftslogik.

---

# CSS

CSS enthält ausschließlich Darstellung.

Keine JavaScript-Logik.

Keine PHP-Logik.

---

# JavaScript

JavaScript dient ausschließlich:

- Interaktion
- Benutzerkomfort
- dynamischen Inhalten

Geschäftslogik bleibt serverseitig.

---

# API

API-Dateien:

- validieren Eingaben
- prüfen Rechte
- rufen zentrale Funktionen auf
- liefern JSON zurück

Keine doppelte Geschäftslogik.

---

# MCP

Auch MCP verwendet ausschließlich zentrale Funktionen.

Es existiert niemals eine zweite Implementierung derselben Logik.

---

# Wiederverwendbarkeit

Wenn drei Module dieselbe Funktion benötigen:

Dann existiert genau eine Funktion.

Nicht drei Kopien.

---

# Lesbarkeit

Lesbarkeit ist wichtiger als Kürze.

Schlecht:

```php
if($a&&$b||$c)
```

Gut:

```php
if (
    $benutzer_aktiv
    && $rechte_vorhanden
)
```

---

# Sicherheit

Immer:

- Eingaben validieren
- SQL vorbereiten
- HTML escapen
- Rechte prüfen
- Sessions absichern

Nie:

- Benutzereingaben direkt verwenden
- SQL zusammensetzen
- Passwörter speichern

---

# Performance

Erst lesbarer Code.

Dann funktionierender Code.

Dann schneller Code.

Keine Optimierung ohne echten Grund.

---

# Erweiterbarkeit

Neue Module sollen:

- bestehende Funktionen nutzen
- bestehende Standards verwenden
- bestehende Architektur respektieren

---

# Verboten

Nicht erlaubt sind:

- doppelte Funktionen
- Copy & Paste Programmierung
- riesige Dateien
- riesige Funktionen
- ungenutzter Code
- tote Variablen
- Hardcodierungen
- globale Variablen ohne Grund
- direkte SQL-Abfragen auf Inhaltsseiten
- direkte API-Aufrufe ohne zentrale Funktionen

---

# Ziel

Am Ende entsteht ein Projekt mit:

- einheitlichem Coding Style
- hoher Wiederverwendbarkeit
- geringer Fehleranfälligkeit
- klarer Architektur
- optimaler Erweiterbarkeit durch Entwickler und KI.

Jeder neue Code muss diese Regeln erfüllen.