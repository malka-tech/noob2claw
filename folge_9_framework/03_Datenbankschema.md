# 03_Datenbankschema.md

# Datenbankschema

Version: 2.0

Datenbanksystem: MariaDB

Dieses Dokument definiert die allgemeinen Regeln für den Aufbau der gesamten Datenbank.

Es beschreibt **nicht** die einzelnen Tabellen, sondern die Standards, nach denen sämtliche Tabellen entwickelt werden.

Alle zukünftigen Datenbanktabellen müssen diesen Regeln entsprechen.

Die eigentlichen Tabellen werden in separaten Dokumenten beschrieben.

Beispiel:

```text
11_Datenbanktabellen_Core.md
```

---

# Ziel

Die Datenbank soll:

- einfach verständlich sein
- leicht wartbar sein
- beliebig erweiterbar sein
- performant sein
- konsistent aufgebaut sein
- optimal durch KI weiterentwickelt werden können

---

# Grundprinzip

Jede Tabelle besitzt genau eine Aufgabe.

Beispiele:

```text
benutzer

benutzer_gruppen

sessions

api_log

mcp_log

agenten

einstellungen
```

Es werden keine Tabellen erstellt, die mehrere Fachbereiche gleichzeitig abdecken.

---

# Fachobjekte

Jede Tabelle repräsentiert genau ein Fachobjekt.

Für jede Tabelle existiert genau eine zentrale Funktionsdatei.

Beispiel

```text
Tabelle

↓

Funktionsdatei
```

```text
benutzer

↓

funktionen_benutzer.php
```

```text
sessions

↓

funktionen_sessions.php
```

```text
einstellungen

↓

funktionen_einstellungen.php
```

Die gesamte Business-Logik greift ausschließlich über diese Funktionsdateien auf die Datenbank zu.

Direkte SQL-Zugriffe außerhalb dieser Dateien sind nicht erlaubt.

---

# Sprache

Alle Tabellen- und Spaltennamen werden auf Deutsch angelegt.

Beispiele

```text
benutzer

benutzer_gruppen

agenten

einstellungen

sessions
```

Nicht

```text
users

roles

agents

settings
```

Technische Begriffe wie

```text
json

api

mcp
```

dürfen übernommen werden.

---

# Schreibweise

Tabellen

- Kleinbuchstaben
- Unterstriche als Worttrenner

Beispiele

```text
benutzer

benutzer_log

api_log

mcp_log

agenten
```

---

# Primärschlüssel

Jede Tabelle besitzt

```text
id
```

Datentyp

```text
BIGINT UNSIGNED
```

Eigenschaften

- AUTO_INCREMENT
- PRIMARY KEY

Es existieren keine anderen Primärschlüssel.

---

# Fremdschlüssel

Fremdschlüssel orientieren sich immer am Tabellennamen.

Beispiele

```text
benutzer_id

agent_id

api_benutzer_id

mcp_benutzer_id

benutzer_gruppe_id
```

Nicht

```text
userid

uid

fk1

id_benutzer
```

---

# Keine NULL-Werte

Im gesamten Projekt werden grundsätzlich keine NULL-Werte verwendet.

Alle Tabellen besitzen feste Standardwerte.

Dadurch wird die Business-Logik erheblich vereinfacht.

---

# Standardwerte

| Datentyp | Standardwert |
|----------|--------------|
| BIGINT | 0 |
| INT | 0 |
| DECIMAL | 0.00 |
| BOOLEAN | 0 |
| VARCHAR | '' |
| TEXT | '' |
| LONGTEXT | '' |
| JSON Objekt | {} |
| JSON Array | [] |
| Zeitstempel | 0 |

---

# Zeitangaben

Zeitangaben werden grundsätzlich als Unix-Timestamp gespeichert.

Datentyp

```text
BIGINT UNSIGNED
```

Beispiele

```text
erstellt_am

geaendert_am

letzte_anmeldung

letzte_aktivitaet

gueltig_bis

beendet_am
```

Der Wert

```text
0
```

bedeutet

```text
nicht gesetzt
```

Dadurch werden Probleme mit

```text
0000-00-00 00:00:00
```

vermieden.

---

# Optionale Beziehungen

Optionale Beziehungen verwenden grundsätzlich

```text
0
```

anstelle von

```text
NULL
```

Beispiele

```text
haupt_id = 0

benutzer_id = 0

agent_id = 0
```

Die Prüfung erfolgt ausschließlich innerhalb der Business-Logik.

---

# Datentypen

## IDs

```text
BIGINT UNSIGNED
```

---

## Ganze Zahlen

```text
INT

BIGINT
```

---

## Kommazahlen

```text
DECIMAL
```

FLOAT und DOUBLE sollen möglichst vermieden werden.

---

## Kurze Texte

```text
VARCHAR
```

---

## Lange Texte

```text
TEXT

LONGTEXT
```

---

## JSON

Komplexe Konfigurationen dürfen als JSON gespeichert werden.

Beispiele

```text
parameter_json

konfiguration_json

widgets_json
```

JSON ersetzt jedoch keine relationalen Tabellen.

---

# Statusfelder

Statusfelder besitzen immer eindeutige Namen.

Beispiele

```text
aktiv

gesperrt

geloescht
```

Datentyp

```text
TINYINT(1)
```

Werte

```text
0 = Nein

1 = Ja
```

---

# Zeitstempel

Nahezu jede Tabelle besitzt

```text
erstellt_am

geaendert_am
```

Optional

```text
letzte_anmeldung

letzte_aktivitaet

beendet_am

gueltig_bis
```

Alle Werte werden als Unix-Timestamp gespeichert.

---

# Benutzerinformationen

Wenn sinnvoll

```text
erstellt_von

geaendert_von
```

Dadurch bleiben Änderungen nachvollziehbar.

---

# Indizes

Indizes werden ausschließlich auf häufig verwendete Suchfelder angelegt.

Beispiele

```text
benutzername

email

aktiv

agent_id

erstellt_am
```

---

# Beziehungen

Es werden ausschließlich echte Beziehungen verwendet.

Beispiel

```text
benutzer

↓

benutzer_gruppen_zuordnung

↓

benutzer_gruppen
```

Nicht

```text
gruppe1

gruppe2

gruppe3
```

---

# Viele-zu-Viele-Beziehungen

Viele-zu-Viele-Beziehungen erhalten immer eigene Tabellen.

Beispiele

```text
benutzer_gruppen_zuordnung

benutzer_gruppen_rechte
```

---

# Keine Redundanzen

Informationen werden grundsätzlich nur einmal gespeichert.

Nicht

```text
gruppenname
```

wenn bereits

```text
benutzer_gruppe_id
```

existiert.

---

# Keine berechneten Werte

Berechenbare Werte werden nicht gespeichert.

Nicht

```text
anzahl_agenten
```

wenn diese jederzeit per SQL ermittelt werden kann.

---

# Soft Delete

Wichtige Stammdaten werden grundsätzlich nicht physisch gelöscht.

Stattdessen

```text
aktiv = 0

oder

geloescht = 1
```

Dadurch bleiben

- Historien
- Logs
- Beziehungen

erhalten.

---

# Einstellungen

Konfigurationen werden grundsätzlich nicht als zusätzliche Datenbankspalten gespeichert.

Es existiert dafür ausschließlich die Tabelle

```text
einstellungen
```

Beispiel

```text
benutzer

key

wert
```

Neue Einstellungen benötigen dadurch keine Datenbankmigration.

Die genaue Funktionsweise wird in

```text
12_Einstellungen.md
```

beschrieben.

---

# Tabellenbeschreibung

Jede neue Tabelle wird nach demselben Schema dokumentiert.

```text
Beschreibung

↓

Aufgabe

↓

CREATE TABLE

↓

Spaltenbeschreibung

↓

Indizes

↓

Beziehungen

↓

Besonderheiten

↓

Verwendete Funktionen

↓

Beispiele
```

---

# SQL-Zugriffe

Auf Datenbanktabellen wird ausschließlich über die zugehörige Funktionsdatei zugegriffen.

Beispiel

```text
benutzer

↓

funktionen_benutzer.php
```

```text
sessions

↓

funktionen_sessions.php
```

```text
einstellungen

↓

funktionen_einstellungen.php
```

Nicht erlaubt

```text
SQL in index.php

SQL in api.php

SQL in mcp.php

SQL in Navigationsseiten
```

---

# Migrationen

Änderungen erfolgen ausschließlich über Migrationen.

Beispiel

```text
20260716_1200_benutzer_erweitern.sql
```

Tabellen werden niemals manuell verändert.

---

# Erweiterbarkeit

Neue Tabellen dürfen ergänzt werden, wenn

- keine bestehende Tabelle verwendet werden kann
- keine Redundanzen entstehen
- die Architektur erhalten bleibt

Neue Tabellen benötigen immer

- Dokumentation
- Funktionsdatei
- CRUD-Funktionen
- Tests

---

# Ziel

Die gesamte Datenbank folgt einer einheitlichen Architektur.

Jede Tabelle besitzt:

- genau eine Aufgabe
- genau eine Funktionsdatei
- klar definierte Beziehungen
- feste Standardwerte
- keine NULL-Werte
- eine vollständige Dokumentation

Dadurch entsteht eine konsistente, wartbare und langfristig erweiterbare Datenbankstruktur, die sowohl für Entwickler als auch für KI-Systeme optimal geeignet ist.