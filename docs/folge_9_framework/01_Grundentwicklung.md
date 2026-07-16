# 01_Grundentwicklung.md

# Grundentwicklung

Version: 1.0

Dieses Dokument definiert die grundlegenden Regeln für die Entwicklung des gesamten Projekts.

Es beschreibt **nicht**, wie einzelne Module funktionieren, sondern legt fest, **wie grundsätzlich entwickelt wird**.

Alle weiteren Dokumente bauen auf diesem Dokument auf.

---

# Ziel

Das Ziel ist die Entwicklung einer modernen, wartbaren und langfristig erweiterbaren Webanwendung.

Dabei gilt:

- die Architektur steht immer über einzelnen Funktionen
- Qualität ist wichtiger als Geschwindigkeit
- Wiederverwendbarkeit ist wichtiger als kurzfristige Lösungen
- möglichst wenig doppelter Code
- möglichst kleine, klar getrennte Komponenten
- jede Datei besitzt genau eine Aufgabe

---

# Entwicklungsphilosophie

Die KI entwickelt den Großteil der Anwendung.

Der Entwickler definiert:

- Architektur
- Regeln
- Datenmodell
- Qualitätsstandards

Die KI setzt diese Regeln anschließend konsequent um.

Die KI soll niemals selbst neue Architekturen erfinden.

---

# Entwicklungsreihenfolge

Neue Funktionen werden immer in folgender Reihenfolge entwickelt:

1. Architektur
2. Datenmodell
3. Verzeichnisstruktur
4. Funktionsstruktur
5. API
6. Benutzeroberfläche
7. Tests
8. Optimierung

Nicht anders herum.

---

# Architektur vor Funktion

Neue Funktionen dürfen niemals direkt implementiert werden.

Vor jeder Entwicklung muss geprüft werden:

- Gibt es bereits passende Funktionen?
- Gibt es bereits passende Module?
- Gibt es bereits passende Tabellen?
- Gibt es bereits passende APIs?
- Gibt es bereits passende Komponenten?

Nur wenn dies nicht der Fall ist, wird erweitert.

---

# Kleine Entwicklungsschritte

Große Änderungen sollen vermieden werden.

Stattdessen:

- kleine Module
- kleine Klassen
- kleine Funktionen
- kleine Commits
- kleine Prompts

Dadurch bleibt die KI kontrollierbar.

---

# Wiederverwendung

Vor jeder neuen Funktion muss geprüft werden:

Kann vorhandener Code verwendet werden?

Neue Funktionen dürfen nur entstehen wenn keine passende Funktion existiert.

Doppelter Code ist grundsätzlich zu vermeiden.

---

# Eine Aufgabe pro Datei

Jede Datei besitzt genau eine Verantwortung.

Beispiele:

✔ Benutzerverwaltung

✔ Navigation

✔ Login

✔ API

✔ Rechte

Nicht:

✘ Benutzer + Login + API + Navigation gleichzeitig

---

# Eine Aufgabe pro Funktion

Funktionen sollen möglichst klein bleiben.

Schlecht:

```php
speichern()
```

Gut:

```php
benutzer_speichern()

rolle_speichern()

agent_speichern()
```

---

# Lesbarkeit vor Cleverness

Code wird für Menschen geschrieben.

Deshalb:

- sprechende Namen
- kurze Funktionen
- klare Struktur
- keine unnötigen Tricks

---

# Erweiterbarkeit

Jede Entwicklung muss davon ausgehen, dass das Projekt später deutlich größer wird.

Neue Bereiche sollen ergänzt werden können ohne bestehende Architektur umzubauen.

---

# Standardisierung

Für wiederkehrende Aufgaben werden Standards definiert.

Zum Beispiel:

- Navigation
- Datenbankzugriffe
- Formulare
- Tabellen
- API
- MCP
- Rechte
- Login
- Logging

Es existiert immer genau ein Standard.

---

# Fehlerbehandlung

Fehler dürfen niemals ignoriert werden.

Jeder Fehler wird:

- erkannt
- protokolliert
- verständlich ausgegeben

Technische Fehlermeldungen dürfen niemals direkt dem Benutzer angezeigt werden.

---

# Sicherheit

Sicherheit besitzt hohe Priorität.

Grundsätzlich gilt:

- Eingaben validieren
- SQL Injection verhindern
- XSS verhindern
- Rechte prüfen
- Sessions absichern
- Logs führen

---

# Performance

Performance wird berücksichtigt.

Aber:

Lesbarer Code ist wichtiger als Mikrooptimierungen.

Erst wenn Funktionen nachweislich langsam sind, werden sie optimiert.

---

# Dokumentation

Neue Module müssen dokumentiert werden.

Neue Tabellen müssen dokumentiert werden.

Neue APIs müssen dokumentiert werden.

Neue MCP-Tools müssen dokumentiert werden.

Die Dokumentation gehört immer zum Projekt.

---

# KI-Regeln

Die KI darf niemals:

- neue Architektur erfinden
- Regeln ignorieren
- doppelte Funktionen erzeugen
- bestehende Standards umgehen

Die KI soll stattdessen:

- vorhandene Komponenten nutzen
- vorhandene Funktionen erweitern
- vorhandene Architektur respektieren

---

# Best Practices

Immer:

✔ kleine Module

✔ kleine Funktionen

✔ Wiederverwendung

✔ klare Namen

✔ zentrale Funktionen

✔ saubere Struktur

✔ dokumentierter Code

✔ einheitliche Standards

---

# Nicht erlaubt

Nicht erlaubt sind:

- doppelte Funktionen
- mehrfacher Datenbankcode
- ungeprüfte Benutzereingaben
- Hardcodierungen
- globale Variablen ohne Grund
- vermischte Verantwortlichkeiten
- riesige Dateien
- riesige Funktionen

---

# Ziel der Entwicklung

Am Ende soll eine Anwendung entstehen, die:

- leicht verständlich ist
- leicht wartbar ist
- leicht erweiterbar ist
- für Menschen nachvollziehbar bleibt
- für KI optimal weiterentwickelt werden kann

Architektur ist wichtiger als Geschwindigkeit.

Qualität ist wichtiger als Quantität.

Wiederverwendbarkeit ist wichtiger als kurzfristige Lösungen.