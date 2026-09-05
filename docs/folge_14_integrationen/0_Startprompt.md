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
- `docs/folge_14_integrationen/1_Integrationsverwaltung.md` als primäre Aufgabenbeschreibung,
- `docs/folge_14_integrationen/2_Cronjob_Einrichtung.md` für die reale Inbetriebnahme.

Verbindlich bleiben insbesondere Architektur, Verzeichnisstruktur, Navigation, Einstellungen, Rechte, Datenbank, Formulare, Tabellen, Sicherheit, API, MCP und Logging.

Grundregeln:

- Business-Logik liegt unter `inc/` oder in der vorhandenen Klassenstruktur.
- Navigationsdateien enthalten keine Business-Logik und kein SQL.
- Vorhandene Loader, Datenbankfunktionen, UI-Komponenten und Sicherheitsfunktionen werden wiederverwendet.
- Es wird kein zweites Einstellungs-, Rechte- oder Logging-System gebaut.
- Da in den Folgen 9 bis 13 noch kein zentraler Anwendungs-Cronjob umgesetzt
  wurde, erstellt Folge 14 den ersten zentralen Noob2Claw-Cron-Einstieg.
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

# 3. Ersten zentralen Anwendungs-Cronjob schaffen

Suche im ganzen Projekt nach `cron`, `cronjob`, `scheduler`, Zeitplanfeldern, Sperren und Laufprotokollen. Kläre:

1. Wie wird ein Cronjob derzeit aufgerufen?
2. Läuft er über PHP-CLI oder einen geschützten HTTP-Endpunkt?
3. Wie werden parallele Läufe verhindert?
4. Wie werden Start, Ende, Status, Dauer und Fehler protokolliert?
5. Wie werden Aufgaben als fällig erkannt?

Die Folgen 9 bis 13 enthalten noch keinen zentralen Anwendungsscheduler. Falls
auch im aktiven Projekt keiner existiert, erstelle in Folge 14 erstmals einen
zentralen CLI-Einstieg `cron.php`. Existiert entgegen der Dokumentation bereits
ein geeigneter zentraler Einstieg, erweitere diesen kompatibel statt einen
zweiten Scheduler daneben zu bauen.

Der neue Einstieg:

- ist ausschließlich für PHP-CLI vorgesehen und lehnt Webaufrufe ab,
- liegt direkt als `/var/www/noobclaw/cron.php` im Projektstamm und nicht in
  einem Unterordner `/public/`,
- lädt denselben Bootstrap und dieselbe Business-Logik wie die Anwendung,
- akzeptiert eine feste Allowlist von Aufgaben, zunächst `integrationen`,
- besitzt eine globale atomare Sperre mit kontrollierter Ablaufzeit,
- liefert verlässliche Exit-Codes,
- schreibt sichere Anwendungslogs ohne Secrets,
- enthält selbst keine Integrationsfachlogik, sondern ruft den Dispatcher auf,
- lässt sich später um weitere zentrale Aufgaben erweitern.

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

# 9. Wetteranzeige im Header

Erweitere den bestehenden globalen Header. Oben rechts wird auf allen geeigneten
angemeldeten Seiten das aktuelle Wetter aus dem als globaler Standard für die
Fähigkeit `wetter` gewählten aktiven Integrationseintrag angezeigt.

Verbindlicher Ablauf:

1. globalen Standard für `wetter` über die zentrale Fähigkeitsauflösung laden,
2. prüfen, dass Eintrag und Integration aktiv sind,
3. das zuletzt erfolgreich gespeicherte normalisierte Wetterergebnis verwenden,
4. mindestens Wetterzustand beziehungsweise passendes Symbol und Temperatur mit
   konfigurierter Einheit anzeigen,
5. Standortbezeichnung, Messzeitpunkt, Aktualitätsstatus und Quelle Open-Meteo
   in einer zugänglichen Detailanzeige, etwa Tooltip oder aufklappbarem Element,
   sichtbar machen.

Der Header löst keinen externen API-Aufruf pro Seitenaufruf aus. Die Anzeige
verwendet die durch manuellen Abruf oder Cronjob gespeicherten Daten. Der Browser
erhält weder API-Key noch Rohantwort. Alle Inhalte werden serverseitig sicher
aufgelöst und bei der Ausgabe escaped. Die Daten werden vor dem Rendern über eine
zentrale Servicefunktion bereitgestellt; das Header-Template enthält weder
Datenbankabfragen noch Integrations- oder API-Geschäftslogik.

Ist kein Wetter-Standard gewählt, ist der gewählte Eintrag deaktiviert oder liegt
noch kein erfolgreicher Abruf vor, zeigt der Header einen neutralen Zustand wie
„Wetter nicht verfügbar“ oder blendet das kompakte Wetterelement kontrolliert
aus. Es gibt keinen stillen Fallback auf einen anderen Eintrag. Ist das letzte
gültige Ergebnis älter als das erlaubte Aktualitätsfenster, darf es weiterhin
angezeigt werden, muss aber gut erkennbar als „veraltet“ markiert sein.

Die Darstellung nutzt die bestehenden Header-, Icon-, Farb- und
Responsive-Komponenten. Sie darf Navigation und Benutzeraktionen auf kleinen
Bildschirmen nicht verdrängen, ist per Tastatur erreichbar und besitzt
verständliche Alternativ- beziehungsweise ARIA-Texte. Wettercodes werden zentral
auf eine begrenzte Liste lokaler Symbole und verständlicher deutscher Texte
abgebildet; von der API gelieferte HTML-, Bild- oder Icon-URLs werden nicht
ungeprüft ausgegeben.

---

# 10. Integrations-Cronjob

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

Speichere alle Zeitpunkte in UTC. Berechne `naechster_lauf_am` vom bisherigen
Solltermin aus, überspringe verpasste Intervalle ohne Nachholschleife und begrenze
jeden Dispatcher-Lauf durch deterministische Reihenfolge, Batchgröße und maximale
Gesamtlaufzeit. Nicht bearbeitete Restmengen bleiben für den nächsten Lauf fällig.
Neben der globalen Sperre benötigt jeder Eintrag einen atomaren Claim mit TTL;
reguläre Freigaben erfolgen in `finally`.

Protokolliere auch den globalen Dispatcher-Lauf. Dafür darf
`integration_eintrag_id` bei `lauf_typ = dispatcher` kontrolliert `NULL` sein oder
eine eigene Dispatcher-Lauftabelle verwendet werden. Erfasst werden Start, Ende,
Dauer, Exit-Code und die Anzahl geprüfter, ausgeführter, übersprungener und
fehlgeschlagener Einträge.

Open-Meteo wird höchstens einmal pro Stunde fällig. Der System-Cronjob darf den Dispatcher jede Minute starten; die Fälligkeitsprüfung entscheidet über die Ausführung.

Implementiere den zentralen Einstieg direkt im Projektwurzelverzeichnis als
`/var/www/noobclaw/cron.php`, ohne Unterordner `/public/`. Der Aufruf
`php cron.php integrationen` startet genau den Integrations-Dispatcher. Unbekannte
Aufgaben, Webaufrufe, fehlgeschlagener Bootstrap und Datenbankfehler enden mit
einem sicheren Fehler und einem Exit-Code ungleich `0`. Kontrollierte Fehler
einzelner Integrationseinträge werden isoliert protokolliert und verhindern die
Bearbeitung anderer fälliger Einträge nicht.

---

# 11. Ersten Cronjob im Video einrichten

Führe die Einrichtung vollständig nach
`docs/folge_14_integrationen/2_Cronjob_Einrichtung.md` durch. Die Folge ist erst
abgeschlossen, wenn neben dem manuellen Test auch ein echter automatisch durch
Cron gestarteter Lauf nachgewiesen wurde.

Ermittle den korrekten realen Aufruf der neu erstellten `cron.php`. Verwende
PHP-CLI, absolute Pfade und standardmäßig die persönliche Crontab des bereits
angemeldeten Benutzers `noobclaw`; hierfür sind weder `sudo` noch ein Wechsel zu
`www-data` erforderlich. Übernimm den
vollständigen geprüften Crontab-Eintrag aus der Einrichtungsanleitung. Passe
Benutzer und Projektpfad an das Zielsystem an. Eine separate Cron-Ausgabelogdatei
wird nicht eingerichtet. Die Doppelstart-Sicherung
erfolgt ausschließlich über die atomaren Anwendungssperren.

Im Video:

1. PHP-Pfad und korrekten Betriebssystembenutzer ermitteln.
2. Befehl zunächst manuell ausführen.
3. Exit-Code und strukturiertes Anwendungslaufprotokoll prüfen.
4. Leserecht auf `cron.php` und benötigte Anwendungsschreibrechte unter diesem Benutzer prüfen.
5. mit `crontab -l` den bisherigen Zustand und doppelte Einträge prüfen.
6. Crontab des richtigen Benutzers öffnen.
7. Eintrag mit eindeutigem Kommentar und absoluten Pfaden anlegen.
8. mit `crontab -l` den exakt gespeicherten Eintrag kontrollieren.
9. nach dem nächsten Lauf Cron-Journal und Anwendungslaufprotokoll prüfen.
10. letzten Versuch, letzten Erfolg und Wetterdaten in der Oberfläche kontrollieren.
11. einen parallelen zweiten manuellen Start als gesperrt nachweisen,
12. einen Fehlerfall zeigen und danach die korrekte Konfiguration wiederherstellen,
13. einen Neustart des Cron-Dienstes nur durchführen, wenn das Zielsystem dies
    nach der Crontab-Änderung tatsächlich verlangt.

Der finale tatsächlich verwendete Cron-Eintrag gehört in den Abschlussbericht. Keine Tokens oder Secrets in Kommandozeile oder Crontab.

---

# 12. Sicherheit und Rechte

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

# 13. Tests

Teste mindestens:

- Navigation und Rechte,
- zwei unabhängige Open-Meteo-Einträge,
- serverseitige Feldvalidierung,
- erfolgreichen und fehlerhaften Verbindungstest,
- normalisierte Ausgabe von `hole_wetter()`,
- manuelle Speicherung der Wetterdaten,
- Erhalt letzter gültiger Daten bei Fehlern,
- Header-Ausgabe aus dem gewählten aktiven Wetter-Standard,
- Aktualisierung der Header-Ausgabe nach einem Wechsel des Wetter-Standards,
- Header-Zustände für fehlende, deaktivierte und veraltete Wetterdaten,
- responsive, tastaturbedienbare Wetteranzeige mit sicherer Ausgabe und Attribution,
- Ausgabe-Escaping für manipulierte Standort- und Wettertexte,
- keinen externen Wetter-API-Aufruf bei normalen Seitenaufrufen,
- gültige Standardauswahl nur aus passenden aktiven Einträgen,
- sichere Behandlung deaktivierter Standards,
- fälligen und nicht fälligen Cron-Eintrag,
- Schutz vor paralleler Doppelverarbeitung,
- Wiederaufnahme nach abgelaufener verwaister Sperre,
- Fehlerisolierung zwischen Einträgen,
- begrenzte Batchgröße, Gesamtlaufzeit und Fehler-Backoff,
- stabiler UTC-Zeitplan ohne Drift oder Nachholschleife,
- Dispatcher-Laufprotokoll mit Exit-Code und Zählerständen,
- sicherer Abbruch bei Datenbank- oder Bootstrapfehler,
- Logs ohne Secrets,
- korrekten Open-Meteo-Modus, Attribution und sichere Behandlung des API-Keys,
- Cron-Ausführung als vorgesehener Systembenutzer,
- PHP-Syntax, Migrationen und Browserdarstellung.

---

# 14. Abschlussbericht

Dokumentiere:

- wiederverwendete Komponenten,
- neue und geänderte Dateien,
- Migrationen,
- Klassenvertrag und Registry,
- Fähigkeiten und Standardauflösung,
- Open-Meteo-Konfiguration und Rückgabeformat,
- Header-Wetteranzeige einschließlich Aktualitäts- und Fehlerzuständen,
- Cronjob-Ablauf, Sperren und Fehlerbehandlung,
- finalen Crontab-Eintrag,
- Rechte,
- Tests und Ergebnisse,
- bekannte Einschränkungen.

Die Aufgabe ist erst abgeschlossen, wenn Integrationsverwaltung, Open-Meteo, globale Standardauswahl, Wetteranzeige oben rechts im Header und zentraler Integrations-Cronjob gemeinsam funktionieren und der reale Cronjob auf dem Server eingerichtet und getestet wurde.
