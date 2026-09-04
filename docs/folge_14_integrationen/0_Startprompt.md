# Noob2Claw – Folge 14: Startprompt
# Wir bauen eine zentrale Integrationsverwaltung mit Open-Meteo

Du arbeitest am bestehenden Projekt Noob2Claw.

In Folge 14 wird das System um eine zentrale Integrationsverwaltung erweitert. Externe Dienste werden als Integrationsklassen eingebunden. Eine Klasse beschreibt den Dienst und seine Fähigkeiten. Zu jeder Klasse können mehrere getrennte Einträge angelegt werden, beispielsweise mehrere OpenAI-kompatible Server oder mehrere Standorte einer Wetterintegration.

Das Ziel ist nicht, Open-Meteo als isolierte Sonderlösung zu bauen.

```text
allgemeines Integrationssystem
    +
mehrere Einträge je Integrationsklasse
    +
globale Standardauswahl je Fähigkeit
    +
zentraler Integrations-Cronjob
    +
erste konkrete Open-Meteo-Integration
```

---

# 1. Repository und Grundlagen

Repository:

```text
https://github.com/malka-tech/noob2claw.git
```

Vorlage:

```text
/home/noobclaw/noob2claw_vorlage/
```

Aktives Projekt:

```text
/mnt/noob2claw/
```

Aktualisiere die Vorlage vorsichtig auf `origin/main`. Bewahre vorhandene lokale Arbeit. Lies danach vollständig:

- sämtliche Markdown-Dateien unter `docs/folge_9_framework/`,
- die relevanten Dokumentationen der Folgen 10 bis 13,
- `docs/folge_14_integrationen/1_Integrationsverwaltung.md` als primäre Aufgabenbeschreibung.

Verbindlich bleiben insbesondere Architektur, Verzeichnisstruktur, Navigation, Einstellungen, Rechte, Datenbank, Formulare, Tabellen, Sicherheit, API, MCP und Logging.

Grundregeln:

- Business-Logik liegt unter `inc/` oder in der vorhandenen Klassenstruktur.
- Navigationsdateien enthalten keine Business-Logik und kein SQL.
- Vorhandene Loader, Datenbankfunktionen, UI-Komponenten und Sicherheitsfunktionen werden wiederverwendet.
- Es wird kein zweites Einstellungs-, Rechte-, Logging- oder Cronjob-System gebaut.
- Geheimnisse erscheinen nie im Klartext in Oberfläche oder Logs.
- Migrationen sind idempotent und nicht destruktiv.

---

# 2. Aktives Projekt analysieren

Untersuche vor Änderungen mindestens:

```text
/mnt/noob2claw/index.php
/mnt/noob2claw/api.php
/mnt/noob2claw/mcp.php
/mnt/noob2claw/inc/
/mnt/noob2claw/nav/
/mnt/noob2claw/css/
/mnt/noob2claw/js/
/mnt/noob2claw/sql/
```

Suche insbesondere nach:

- Cronjobs, Scheduler, Locks, Laufzeiten und Logs,
- Klassen, Autoloading und Funktionsloader,
- Navigation und Unterseiten,
- allgemeinen Einstellungen,
- Rechten und CSRF-Schutz,
- Verschlüsselung beziehungsweise Secret-Speicherung,
- zentralen HTTP-Aufrufen, Timeouts und Fehlerbehandlung,
- Datenbankmigrationen,
- Status-, Test- und Protokollansichten.

Dokumentiere kurz, welche vorhandenen Strukturen wiederverwendet werden.

---

# 3. Bestehende Cronjob-Technik weiterverwenden

Suche im ganzen Projekt nach `cron`, `cronjob`, `scheduler`, Zeitplanfeldern, Sperren und Laufprotokollen. Kläre:

1. Wie wird ein Cronjob derzeit aufgerufen?
2. Läuft er über PHP-CLI oder einen geschützten HTTP-Endpunkt?
3. Wie werden parallele Läufe verhindert?
4. Wie werden Start, Ende, Status, Dauer und Fehler protokolliert?
5. Wie werden Aufgaben als fällig erkannt?

Erweitere genau diese Technik. Baue keinen unabhängigen zweiten Scheduler. Falls nur ein einfacher zentraler Cronjob existiert, ergänze dort einen getrennten Integrations-Dispatcher.

---

# 4. Pflichtziele

1. Neuer Navigationspunkt „Integrationen“ im Bereich „Einstellungen“.
2. Zentrale serverseitige Registry für erlaubte Integrationsklassen.
3. Verbindlicher Klassenvertrag mit allgemeinen Methoden.
4. Unterstützung deklarierter integrationsspezifischer Fähigkeiten.
5. Mehrere Einträge je Integrationsklasse.
6. Flexible Einstellungsseiten und optionale eigene Unterseiten.
7. Globale Standardintegration je Fähigkeit.
8. Open-Meteo als erste Integration.
9. Stündliche Abholung aktueller Wetterdaten.
10. Allgemeiner Integrations-Cronjob für fällige Integrationsjobs.
11. Sichere Verbindungstests und manuelle Sofortausführung.
12. Rechte, Logging, Fehlerbehandlung, Migrationen und Tests.
13. Einrichtung des realen System-Cronjobs im Video.

---

# 5. Integrationsvertrag

Jede Integration ist eine Klasse und stellt mindestens bereit:

```php
informationen(): array
einstellungen(): array
datenabholung(int $eintrag_id, array $kontext = []): array
cronjob(int $eintrag_id, array $kontext = []): array
```

Die Signaturen dürfen an bestehende Projektstandards angepasst werden, nicht aber ihre Bedeutung:

- `informationen()` liefert stabile Metadaten, Fähigkeiten und Versionsinformationen.
- `einstellungen()` liefert ein deklaratives Schema für Felder, erlaubte Aktionen und optionale eigene Navigationspunkte innerhalb der Integrationsverwaltung.
- `datenabholung()` führt die normale Abholung für genau einen Eintrag aus.
- `cronjob()` führt die zeitgesteuerte Arbeit für genau einen Eintrag aus.

Integrationen können spezifische Fähigkeiten anbieten, zum Beispiel:

```php
hole_wetter(int $eintrag_id, array $optionen = []): array
```

Diese Fähigkeit muss in `informationen()` deklariert werden. Das System darf nie beliebige Methoden aus Benutzereingaben ausführen.

---

# 6. Mehrere Einträge je Klasse

Eine Klasse ist der Integrationstyp; ein Eintrag ist eine konkrete Konfiguration:

```text
Klasse: OpenAI-kompatible API
├── Eintrag: lokaler LM-Studio-Server
├── Eintrag: Firmen-vLLM-Server
└── Eintrag: externer Anbieter
```

Jeder Eintrag besitzt mindestens ID, Integrationsschlüssel, Bezeichnung, Aktivstatus, Konfigurationswerte, geschützte Secrets, Cronjob-Status und -Intervall, letzten Versuch, letzten Erfolg, nächsten Lauf, letzten Status, sichere Fehlermeldung sowie Erstellungs- und Änderungsdaten.

Anlegen, Bearbeiten, Aktivieren, Deaktivieren, Archivieren beziehungsweise sicheres Löschen, Verbindung testen und Daten jetzt abrufen müssen nach vorhandenem Rechte- und UI-Konzept funktionieren.

---

# 7. Globale Standardintegrationen

Erstelle eine Maske für systemweite Fähigkeiten wie Wetter, Sprachmodell, Text-to-Speech, Speech-to-Text, Suche und Benachrichtigungen.

Die Optionen entstehen dynamisch aus den deklarierten Fähigkeiten aktiver Einträge. Ein Standard verweist immer auf einen konkreten Eintrag, nicht nur auf eine Klasse. Deaktivieren oder Löschen eines verwendeten Standards muss verhindert oder bewusst aufgelöst werden. Es gibt keinen stillen Fallback auf einen beliebigen Eintrag.

Stelle eine zentrale Funktion bereit, über die andere Module den Standard für eine Fähigkeit auflösen und kontrolliert aufrufen können.

---

# 8. Open-Meteo-Integration

Implementiere Open-Meteo anhand der offiziellen Dokumentation:

```text
https://open-meteo.com/en/docs
```

Mindestens erforderlich:

- Fähigkeit `wetter`,
- Bezeichnung, Breitengrad, Längengrad und Zeitzone,
- auswählbare Einheiten für Temperatur, Wind und Niederschlag,
- Aktiv- und Cronjob-Status,
- Verbindungstest und manuelle Datenabholung,
- `hole_wetter()`, `datenabholung()` und `cronjob()`.

Unterstütze einen klaren Nutzungsmodus:

- kostenfreie Open-Access-API für nicht-kommerzielle Nutzung,
- Kunden-API für kommerzielle Nutzung mit sicher gespeichertem API-Key.

Die Endpunkte werden aus einer fest definierten serverseitigen Allowlist gewählt und niemals frei eingegeben. Zeige einen Hinweis auf die jeweils aktuellen Nutzungsbedingungen, Limits und die erforderliche Quellenangabe. Wetterdaten müssen in der Oberfläche mit einer sichtbaren Attribution zu Open-Meteo versehen werden. Prüfe vor der Implementierung erneut:

```text
https://open-meteo.com/en/terms
https://open-meteo.com/en/pricing
```

Verwende `/v1/forecast` mit `latitude`, `longitude`, `current`, `timezone`, `temperature_unit`, `wind_speed_unit` und `precipitation_unit`.

Mindestens abzurufen:

```text
temperature_2m
relative_humidity_2m
apparent_temperature
is_day
precipitation
weather_code
cloud_cover
pressure_msl
wind_speed_10m
wind_direction_10m
wind_gusts_10m
```

Validiere Koordinaten, Zeitzone, Einheiten, HTTP-Status, Content-Type und JSON-Struktur. Nutze HTTPS, angemessene Timeouts und eine begrenzte Antwortgröße. Bei Fehlern bleiben die letzten gültigen Daten bestehen und werden als veraltet markiert; sie dürfen nicht mit leeren Werten überschrieben werden.

Speichere normalisierte Wetterfelder und, sofern sinnvoll, eine größenbegrenzte Rohantwort zur Diagnose.

---

# 9. Integrations-Cronjob

```text
zentraler Cron-Aufruf
    → globale Sperre setzen
    → fällige aktive Einträge laden
    → Klasse aus Registry auflösen
    → cronjob(eintrag_id) aufrufen
    → Ergebnis und Laufzeit protokollieren
    → nächsten Lauf berechnen
    → Sperre zuverlässig lösen
```

Pflichtregeln:

- nur registrierte Klassen und aktive Einträge,
- atomare Sperren gegen parallele Läufe,
- Ablaufzeit für verwaiste Sperren,
- unabhängige Fehlerbehandlung je Eintrag,
- Begrenzung von Laufzeit und Aufgabenanzahl,
- konsistente Zeitbasis,
- Protokollierung von Start, Ende, Status, Dauer, Eintrag und sicherem Fehler,
- keine Secrets oder vollständigen sensitiven Antworten in Logs,
- stabiler Zeitplan ohne unkontrolliertes Driften,
- gleiche Business-Logik für manuelle und geplante Aufrufe.

Open-Meteo wird höchstens einmal pro Stunde fällig. Der System-Cronjob darf den Dispatcher jede Minute starten; die Fälligkeitsprüfung entscheidet über die Ausführung.

---

# 10. Cronjob im Video einrichten

Ermittle den korrekten realen Aufruf aus der bestehenden Anwendung. Bevorzuge PHP-CLI, absolute Pfade und den vorhandenen Webserver-Benutzer. Beispielhaft, nicht blind übernehmen:

```cron
* * * * * /usr/bin/php /var/www/noobclaw/cron.php integrations >> /var/log/noob2claw-integrationen-cron.log 2>&1
```

Falls bereits ein zentraler Cron-Einstieg existiert, muss genau dieser erweitert und verwendet werden.

Im Video:

1. PHP-Pfad und korrekten Betriebssystembenutzer ermitteln.
2. Befehl zunächst manuell ausführen.
3. Exit-Code und Anwendungslog prüfen.
4. Crontab des richtigen Benutzers öffnen.
5. Eintrag mit absoluten Pfaden anlegen.
6. doppelte Einträge ausschließen.
7. nach dem nächsten Lauf Cron- und Integrationslogs prüfen.
8. letzten Versuch, letzten Erfolg und Wetterdaten in der Oberfläche kontrollieren.
9. einen Fehlerfall zeigen und danach die korrekte Konfiguration wiederherstellen.

Der finale tatsächlich verwendete Cron-Eintrag gehört in den Abschlussbericht. Keine Tokens oder Secrets in Kommandozeile, Crontab oder Logumleitung.

---

# 11. Sicherheit und Rechte

Nutze vorhandene Rechte oder ergänze idempotent sinngemäß:

```text
integrationen_anzeigen
integrationen_verwalten
integrationen_eintraege_verwalten
integrationen_standards_verwalten
integrationen_testen
integrationen_cron_ausfuehren
integrationen_logs_anzeigen
```

Administratoren erhalten die neuen Rechte, andere Rollen nicht automatisch.

Verbindlich sind CSRF-Schutz, serverseitige Validierung, parametrisierte SQL-Abfragen, Ausgabe-Escaping, eine Klassen-Allowlist, SSRF-Schutz durch fest definierte API-Basisadressen, zentrale Secret-Speicherung und ein geschützter Cron-Aufruf. Leere Secret-Felder beim Bearbeiten bedeuten „unverändert“.

---

# 12. Tests

Teste mindestens:

- Navigation und Rechte,
- zwei unabhängige Open-Meteo-Einträge,
- serverseitige Feldvalidierung,
- erfolgreichen und fehlerhaften Verbindungstest,
- normalisierte Ausgabe von `hole_wetter()`,
- manuelle Speicherung der Wetterdaten,
- Erhalt letzter gültiger Daten bei Fehlern,
- gültige Standardauswahl nur aus passenden aktiven Einträgen,
- sichere Behandlung deaktivierter Standards,
- fälligen und nicht fälligen Cron-Eintrag,
- Schutz vor paralleler Doppelverarbeitung,
- Fehlerisolierung zwischen Einträgen,
- Logs ohne Secrets,
- korrekten Open-Meteo-Modus, Attribution und sichere Behandlung des API-Keys,
- Cron-Ausführung als vorgesehener Systembenutzer,
- PHP-Syntax, Migrationen und Browserdarstellung.

---

# 13. Abschlussbericht

Dokumentiere:

- wiederverwendete Komponenten,
- neue und geänderte Dateien,
- Migrationen,
- Klassenvertrag und Registry,
- Fähigkeiten und Standardauflösung,
- Open-Meteo-Konfiguration und Rückgabeformat,
- Cronjob-Ablauf, Sperren und Fehlerbehandlung,
- finalen Crontab-Eintrag,
- Rechte,
- Tests und Ergebnisse,
- bekannte Einschränkungen.

Die Aufgabe ist erst abgeschlossen, wenn Integrationsverwaltung, Open-Meteo, globale Standardauswahl und zentraler Integrations-Cronjob gemeinsam funktionieren und der reale Cronjob auf dem Server eingerichtet und getestet wurde.
