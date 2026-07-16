# 12_Einstellungen.md

# Einstellungen und `funktionen_einstellungen.php`

Version: 1.0

## Ziel

Die Tabelle `einstellungen` dient als zentrale Key-Value-Konfiguration der gesamten Anwendung.

Über diese Tabelle werden gespeichert:

- Systemeinstellungen
- Benutzereinstellungen
- API-Einstellungen
- MCP-Einstellungen
- Agenteneinstellungen
- Dashboard-Konfigurationen
- JSON-Konfigurationen
- zukünftige Module

Es werden **keine** neuen Datenbankspalten für einzelne Einstellungen angelegt.

---

# Tabellenstruktur

```text
einstellungen
```

| Spalte | Typ | Beschreibung |
|--------|-----|--------------|
| id | BIGINT | Primärschlüssel |
| benutzer | VARCHAR | Gültigkeitsbereich (`system`, `benutzer:15`, `api:3`, `mcp:2`, `agent:8`) |
| key | VARCHAR | Eindeutiger Schlüssel |
| wert | LONGTEXT | Wert (Text, Zahl oder JSON) |
| datentyp | VARCHAR | string, integer, boolean, decimal, json, text |
| beschreibung | TEXT | Beschreibung |
| erstellt_am | BIGINT | Unix-Timestamp |
| geaendert_am | BIGINT | Unix-Timestamp |

Unique:

```text
benutzer + key
```

---

# Gültigkeitsbereiche

## Global

```text
system
```

Beispiele

```text
system.name
system.debug
system.logo
```

---

## Benutzer

```text
benutzer:15
```

Beispiele

```text
design.modus
navigation.eingeklappt
dashboard.widgets
```

---

## API

```text
api:3
```

---

## MCP

```text
mcp:7
```

---

## Agent

```text
agent:12
```

---

# Datentypen

```text
string
integer
decimal
boolean
json
text
```

JSON wird als LONGTEXT gespeichert und mittels `json_encode()` bzw. `json_decode()` verarbeitet.

---

# Lade-Reihenfolge

Beim Laden einer Einstellung wird automatisch folgende Reihenfolge verwendet:

```text
1. Benutzerwert

↓

2. Systemwert

↓

3. Standardwert aus dem Code
```

Die aufrufende Funktion muss diesen Ablauf **nicht** selbst implementieren.

---

# funktionen_einstellungen.php

Alle Einstellungen werden ausschließlich über diese Datei verarbeitet.

Direkte SQL-Zugriffe auf die Tabelle `einstellungen` sind außerhalb dieser Datei nicht erlaubt.

---

# Öffentliche Funktionen

## Einstellung laden

```php
einstellung_laden(
    string $benutzer,
    string $key,
    mixed $standardwert = ""
);
```

Lädt automatisch:

1. Benutzerwert
2. Systemwert
3. Standardwert

---

## Einstellung speichern

```php
einstellung_speichern(
    string $benutzer,
    string $key,
    mixed $wert,
    string $datentyp = "string",
    string $beschreibung = ""
);
```

Erstellt oder aktualisiert eine Einstellung.

---

## Einstellung löschen

```php
einstellung_loeschen(
    string $benutzer,
    string $key
);
```

---

## Existiert Einstellung

```php
einstellung_existiert(
    string $benutzer,
    string $key
);
```

---

## Alle Einstellungen laden

```php
einstellungen_array(
    string $benutzer
);
```

Lädt sämtliche Einstellungen eines Gültigkeitsbereiches als Array.

---

## JSON laden

```php
einstellung_json_laden(
    string $benutzer,
    string $key,
    array $standard = []
);
```

Lädt den Wert und dekodiert automatisch JSON.

---

## JSON speichern

```php
einstellung_json_speichern(
    string $benutzer,
    string $key,
    array $daten
);
```

Kodiert automatisch mittels `json_encode()`.

---

# Beispiele

## Systemname

```php
$name = einstellung_laden(
    "system",
    "system.name",
    "NoobClaw"
);
```

---

## Benutzerdesign

```php
$modus = einstellung_laden(
    "benutzer:15",
    "design.modus",
    "dunkel"
);
```

---

## Dashboard

```php
$widgets = einstellung_json_laden(
    "benutzer:15",
    "dashboard.widgets",
    []
);
```

---

# Regeln

- Keine direkten SQL-Zugriffe außerhalb von `funktionen_einstellungen.php`
- Keine Hardcodierung von Einstellungen
- Alle Module verwenden ausschließlich die zentralen Funktionen
- JSON wird ausschließlich über die JSON-Funktionen verarbeitet
- Neue Einstellungen benötigen keine Datenbankmigration

---

# Vorteile

- Eine zentrale Konfigurationsverwaltung
- Keine zusätzlichen Tabellen für Konfigurationen
- Benutzer-, API-, MCP- und Agenteneinstellungen mit derselben Struktur
- Automatischer Fallback auf Systemwerte
- Einfache Erweiterbarkeit
- Einheitliche Programmierschnittstelle