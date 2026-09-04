# Noob2Claw – Folge 14: Integrationsverwaltung

Version: 1.1

Dieses Dokument definiert die fachliche und technische Zielarchitektur für die globale Verwaltung externer Integrationen. Es ergänzt die verbindlichen Vorgaben aus Folge 9 und baut auf dem realen Projektstand der Folgen 10 bis 13 auf.

---

# 1. Fachliches Modell

Das Integrationssystem trennt drei Ebenen:

```text
Integrationsklasse
    beschreibt Dienst und Fähigkeiten
             │
             ├── Integrationseintrag A
             ├── Integrationseintrag B
             └── Integrationseintrag C
                         │
                         └── kann globaler Standard
                             für eine Fähigkeit sein
```

Eine Integrationsklasse ist ein serverseitiger technischer Adapter, etwa Open-Meteo oder eine OpenAI-kompatible API. Ein Integrationseintrag ist eine konkrete Konfiguration, etwa „Wetter Berlin“ oder „lokaler LLM-Server“.

Eine Fähigkeit ist ein stabiler technischer Schlüssel wie:

```text
wetter
sprachmodell
text_to_speech
speech_to_text
suche
benachrichtigung
```

Fähigkeiten sind nicht dasselbe wie beliebige Methoden. Sie werden in den Metadaten deklariert und zentral validiert.

---

# 2. Klassenvertrag

Alle Integrationsklassen implementieren einen gemeinsamen Vertrag. Ob Interface, abstrakte Basisklasse oder beides eingesetzt wird, richtet sich nach der vorhandenen Klassenstruktur.

```php
public function informationen(): array;
public function einstellungen(): array;
public function datenabholung(int $eintrag_id, array $kontext = []): array;
public function cronjob(int $eintrag_id, array $kontext = []): array;
```

Empfohlene zentrale Hilfen:

```php
protected function eintrag_laden(int $eintrag_id): array;
protected function konfiguration_laden(int $eintrag_id): array;
protected function geheimnis_laden(int $eintrag_id, string $schluessel): string;
protected function http_anfrage(array $anfrage): array;
protected function lauf_starten(int $eintrag_id, string $typ): int;
protected function lauf_beenden(int $lauf_id, array $ergebnis): void;
```

## `informationen()`

Liefert stabile Metadaten:

```php
[
    'schluessel' => 'open_meteo',
    'titel' => 'Open-Meteo',
    'beschreibung' => 'Wetterdaten über die Open-Meteo Forecast API',
    'version' => '1.0.0',
    'anbieter_url' => 'https://open-meteo.com/',
    'dokumentation_url' => 'https://open-meteo.com/en/docs',
    'icon' => 'cloud-sun',
    'mehrere_eintraege' => true,
    'faehigkeiten' => [
        'wetter' => [
            'methode' => 'hole_wetter',
            'beschreibung' => 'Liefert aktuelle Wetterdaten',
        ],
    ],
    'cronjob' => [
        'verfuegbar' => true,
        'standard_intervall_sekunden' => 3600,
    ],
]
```

## `einstellungen()`

Liefert ein deklaratives Schema, aus dem die allgemeine Verwaltung Formulare rendert. Unterstützt werden mindestens Schlüssel, Bezeichnung, Hilfetext, Feldtyp, Pflichtfeld, Standardwert, Validierung, Auswahloptionen, Secret-Kennzeichnung, Abhängigkeiten und Feldgruppen.

Eine Integration darf außerdem eigene Navigationspunkte innerhalb ihrer Detailverwaltung deklarieren, etwa „Einstellungen“, „Abgerufene Daten“, „Modelle laden“ oder „Verbindung testen“. Die zentrale Verwaltung prüft Schlüssel, Ziel, benötigtes Recht und Sortierung gegen die Klassendefinition. Keine frei übergebenen Dateien, URLs oder Callbacks.

## `datenabholung()` und `cronjob()`

`datenabholung()` führt eine externe Abfrage für einen konkreten Eintrag aus. `cronjob()` erledigt dessen geplante Arbeit und darf `datenabholung()` verwenden. Planung, Fälligkeit, Sperren und übergreifendes Logging bleiben zentral.

## Spezifische Methoden

Open-Meteo stellt zusätzlich bereit:

```php
public function hole_wetter(int $eintrag_id, array $optionen = []): array;
```

Andere Module greifen bevorzugt über die zentrale Fähigkeitsauflösung darauf zu.

---

# 3. Registry

Eine zentrale serverseitige Registry ordnet stabile Integrationsschlüssel erlaubten Klassen zu:

```php
[
    'open_meteo' => OpenMeteoIntegration::class,
]
```

Pflichtanforderungen:

- eindeutige Schlüssel,
- Prüfung des Klassenvertrags,
- keine Instanziierung beliebiger Request-Werte,
- verständlicher Fehler bei fehlender oder ungültiger Klasse,
- neue Integrationen ohne Umbau der Verwaltung ergänzbar.

Ein vorhandener sicherer Loader darf verwendet werden. Reine Dateierkennung ersetzt nicht die Vertragsprüfung.

---

# 4. Rückgabeformat

Alle Aufrufe liefern ein einheitliches Ergebnis:

```php
[
    'erfolg' => true,
    'status' => 'erfolgreich',
    'meldung' => 'Wetterdaten wurden aktualisiert.',
    'daten' => [],
    'fehlercode' => '',
    'wiederholbar' => false,
    'quelle' => 'open_meteo',
    'eintrag_id' => 12,
    'abgerufen_am' => '2026-09-03T12:00:00+00:00',
    'dauer_ms' => 184,
]
```

Fehler geben keine Secrets, vollständigen Header, Stacktraces oder internen Pfade an normale Benutzer aus.

---

# 5. Datenmodell

Vorhandene passende Tabellen werden erweitert. Wenn keine existieren, wird mindestens dieses logische Modell benötigt. Namen folgen den Projektkonventionen.

## `integrationen_eintraege`

```text
id
integrationsschluessel
bezeichnung
beschreibung
aktiv
cronjob_aktiv
cronjob_intervall_sekunden
letzter_versuch_am
letzter_erfolg_am
naechster_lauf_am
letzter_status
letzter_fehlercode
letzte_fehlermeldung
gesperrt_bis
erstellt_von
erstellt_am
geaendert_von
geaendert_am
archiviert
```

Indexe werden für Integrationsschlüssel, Aktivstatus und Fälligkeit benötigt.

## `integrationen_werte`

```text
id
integration_eintrag_id
schluessel
wert
ist_geheimnis
erstellt_am
geaendert_am
```

Eindeutig: `integration_eintrag_id + schluessel`.

Secrets werden über vorhandene zentrale Verschlüsselung gespeichert. Falls diese fehlt, wird eine zentrale Secret-Technik mit außerhalb der Datenbank gespeichertem Schlüssel geschaffen. Hashing reicht für später benötigte API-Schlüssel nicht aus.

## `integrationen_standards`

```text
id
faehigkeit
integration_eintrag_id
erstellt_von
erstellt_am
geaendert_von
geaendert_am
```

Eindeutig: `faehigkeit`.

## `integrationen_laeufe`

```text
id
integration_eintrag_id
integrationsschluessel
lauf_typ
gestartet_am
beendet_am
dauer_ms
status
fehlercode
fehlermeldung
datensaetze
request_id
ausgeloest_durch_typ
ausgeloest_durch_id
```

`lauf_typ` unterscheidet mindestens `cronjob`, `manuell`, `verbindungstest` und `datenabholung`.

Globale Dispatcher-Läufe werden ebenfalls nachvollziehbar gespeichert. Entweder
ist `integration_eintrag_id` für `lauf_typ = dispatcher` bewusst `NULL`, oder es
wird eine eigene Tabelle `integrationen_dispatcher_laeufe` verwendet. Erforderlich
sind mindestens Start, Ende, Dauer, Status, Exit-Code, Zahl geprüfter,
ausgeführter, übersprungener und fehlgeschlagener Einträge sowie eine sichere
Fehlermeldung. Ein künstlicher Integrationseintrag für den Dispatcher ist nicht
zulässig.

## Wetterdaten

Falls keine passende Struktur existiert, werden mindestens gespeichert:

```text
integration_eintrag_id
messzeitpunkt
abgerufen_am
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
einheiten_json
rohantwort_json
ist_aktuell
```

Die Historie wird sinnvoll begrenzt. Das letzte gültige Ergebnis bleibt immer verfügbar.

---

# 6. Verwaltungsoberfläche

Neuer Punkt im Bereich `Settings`:

```text
Integrationen
```

Die Übersicht zeigt verfügbare Klassen, Zahl aktiver und gesamter Einträge, Fähigkeiten, Cronjob-Unterstützung und letzten Status.

Die Eintragsliste zeigt Bezeichnung, Aktivstatus, Fähigkeiten, Cronjob-Status, letzten Versuch, letzten Erfolg, nächsten Lauf, Status und Aktionen.

Die Detailmaske entsteht aus `einstellungen()` und bietet nach Rechten:

- speichern,
- Verbindung testen,
- Daten jetzt abrufen,
- aktivieren oder deaktivieren,
- archivieren beziehungsweise sicher löschen,
- Laufprotokolle anzeigen,
- erlaubte integrationsspezifische Unterseiten.

Eine Unterseite „Standards“ zeigt jede bekannte Fähigkeit. Je Fähigkeit kann genau ein kompatibler aktiver Eintrag oder bewusst kein Standard gewählt werden.

Eine Cronjob-Statusseite zeigt letzten Dispatcher-Lauf, Dauer, ausgeführte, übersprungene und fehlgeschlagene Einträge, nächste Fälligkeiten, nötigen System-Cronjob und filterbare Laufprotokolle.

---

# 7. Open-Meteo

```text
Schlüssel: open_meteo
Titel: Open-Meteo
Fähigkeit: wetter
Mehrere Einträge: ja
Cronjob: ja
Standardintervall: 3600 Sekunden
API: https://api.open-meteo.com/v1/forecast
Dokumentation: https://open-meteo.com/en/docs
```

Die API-Basisadressen sind fest in der Klasse definiert und nicht frei änderbar:

```text
Open Access: https://api.open-meteo.com/v1/forecast
Kunden-API:  https://customer-api.open-meteo.com/v1/forecast
```

Die Auswahl erfolgt ausschließlich über den konfigurierten Nutzungsmodus. Ein benutzerdefinierter Host ist nicht erlaubt.

## Einstellungen

- Bezeichnung,
- Nutzungsmodus `open_access` oder `kunde`,
- API-Key als Secret, nur im Kundenmodus sichtbar und erforderlich,
- Breitengrad `-90` bis `90`,
- Längengrad `-180` bis `180`,
- IANA-Zeitzone oder `auto`,
- Temperatur `celsius` oder `fahrenheit`,
- Wind `kmh`, `ms`, `mph` oder `kn`,
- Niederschlag `mm` oder `inch`,
- Aktivstatus,
- Cronjob-Aktivstatus,
- Intervall mit sicherem Minimum von einer Stunde.

Die Oberfläche erklärt, dass die Open-Access-API nach den jeweils aktuellen Bedingungen nur für nicht-kommerzielle Nutzung vorgesehen ist. Für kommerzielle Nutzung ist die Kunden-API zu verwenden. In jeder sichtbaren Wetterausgabe wird Open-Meteo angemessen als Quelle genannt. Links zu Bedingungen und Lizenz:

```text
https://open-meteo.com/en/terms
https://open-meteo.com/en/pricing
https://open-meteo.com/en/licence
```

Limits und Vertragsdetails werden nicht dauerhaft im Code fest verdrahtet. Die Verwaltung verweist auf die aktuellen offiziellen Angaben.

## API-Anfrage

```text
GET https://api.open-meteo.com/v1/forecast
    ?latitude=52.52
    &longitude=13.41
    &current=temperature_2m,relative_humidity_2m,apparent_temperature,is_day,precipitation,weather_code,cloud_cover,pressure_msl,wind_speed_10m,wind_direction_10m,wind_gusts_10m
    &timezone=Europe/Berlin
    &temperature_unit=celsius
    &wind_speed_unit=kmh
    &precipitation_unit=mm
```

Parameter werden validiert und URL-kodiert. HTTP-Status, Content-Type, Größenlimit und JSON-Struktur werden geprüft. HTTPS und zentrale HTTP-Funktionen mit angemessenen Verbindungs- und Gesamt-Timeouts sind Pflicht.

## Normalisierte Ausgabe von `hole_wetter()`

```php
[
    'anbieter' => 'open_meteo',
    'eintrag_id' => 12,
    'bezeichnung' => 'Berlin',
    'breitengrad' => 52.52,
    'laengengrad' => 13.41,
    'zeitzone' => 'Europe/Berlin',
    'messzeitpunkt' => '2026-09-03T14:00',
    'abgerufen_am' => '2026-09-03T12:00:10+00:00',
    'temperatur' => 21.4,
    'gefuehlte_temperatur' => 20.9,
    'luftfeuchtigkeit' => 58,
    'niederschlag' => 0.0,
    'wettercode' => 1,
    'bewoelkung' => 24,
    'luftdruck' => 1017.2,
    'windgeschwindigkeit' => 11.3,
    'windrichtung' => 245,
    'windboeen' => 18.7,
    'ist_tag' => true,
    'einheiten' => [],
    'veraltet' => false,
]
```

Vor Speicherung werden `current`, `current_units` und benötigte Schlüssel geprüft. Unbekannte zusätzliche Felder dürfen ignoriert werden.

Bei DNS-, TLS-, Verbindungs-, Timeout-, HTTP-, JSON- oder Schemafehlern bleibt das letzte gültige Ergebnis bestehen und wird als veraltet kenntlich gemacht. Logs enthalten Diagnose, aber keine unnötige vollständige Antwort.

---

# 8. Globale Fähigkeitsauflösung

Andere Module instanziieren Integrationsklassen nicht direkt. Eine zentrale Funktion löst den Standard auf:

```php
integration_standard_aufrufen('wetter', 'hole_wetter', $optionen);
```

Spätere spezialisierte Fähigkeitsdienste dürfen diesen Dispatcher erweitern.
Dabei muss `integration_standard_aufrufen()` als kompatibler Wrapper erhalten
bleiben, damit vorhandene Aufrufer nicht gebrochen und keine parallelen
Aufrufarchitekturen geschaffen werden.

Ablauf:

1. Standard-Eintrag laden.
2. Aktivstatus prüfen.
3. Klasse aus Registry laden.
4. deklarierte Fähigkeit und Methode prüfen.
5. Recht beziehungsweise Systemkontext prüfen.
6. Methode mit konkreter Eintrag-ID aufrufen.
7. einheitliches Ergebnis zurückgeben und Fehler protokollieren.

Ohne gültigen Standard entsteht ein klarer fachlicher Fehler. Es wird nicht der erste verfügbare Eintrag verwendet.

---

# 9. Cronjob-Architektur

Die konkrete Installation, Inbetriebnahme, Verifikation und Fehlerbehebung ist in
`docs/folge_14_integrationen/2_Cronjob_Einrichtung.md` verbindlich beschrieben.

Die Folgen 9 bis 13 liefern noch keinen zentralen Anwendungsscheduler. Folge 14
führt deshalb erstmals einen zentralen PHP-CLI-Einstieg `cron.php` ein. Dieser
kennt nur erlaubte Aufgabenschlüssel und stößt mit `integrationen` den
Integrations-Dispatcher an. Fachlogik bleibt in den zentralen Funktionen unter
`inc/` und wird nicht in `cron.php` dupliziert.

Der Einstieg lehnt HTTP-Aufrufe ab, lädt den vorhandenen Bootstrap, erwirbt eine
globale atomare Sperre, liefert definierte Exit-Codes und protokolliert ohne
Secrets. Ein optionales Betriebssystem-`flock` ergänzt diese Sperre, ersetzt sie
aber nicht. Spätere Folgen erweitern denselben Einstieg um neue Aufgaben, statt
weitere Cron-Dateien oder Scheduler zu schaffen.

Ein Eintrag ist fällig, wenn er und sein Cronjob aktiv sind, die Klasse vorhanden ist, sie Cronjobs unterstützt, der nächste Lauf erreicht wurde und keine gültige Sperre besteht.

Sperren werden atomar erworben. Einfaches „lesen, dann schreiben“ ohne atomare Aktualisierung oder Transaktion reicht nicht. Verwaiste Sperren besitzen ein kontrolliertes Ablaufdatum.

Jeder Eintrag läuft in einem eigenen Fehlerkontext. Ein Fehler bei „Wetter Berlin“ darf „Wetter München“ oder andere Integrationen nicht stoppen.

Statuswerte mindestens:

```text
gestartet
erfolgreich
fehlgeschlagen
uebersprungen
gesperrt
```

Der Dispatcher kann jede Minute laufen. Open-Meteo besitzt mindestens 3600 Sekunden Intervall. Nach Erfolg wird der nächste stabile Planzeitpunkt berechnet. Fehler nutzen einen begrenzten vorhandenen Backoff oder Retry ohne Endlosschleife.

Zeitplanung und Lastbegrenzung:

- Datenbankzeiten werden einheitlich in UTC gespeichert.
- `naechster_lauf_am` wird vom vorherigen Solltermin aus weitergerechnet, nicht
  einfach vom Abschlusszeitpunkt; dadurch driftet der Plan nicht.
- Verpasste Intervalle werden bis zum nächsten zukünftigen Solltermin
  übersprungen und nicht in einer unkontrollierten Nachholschleife ausgeführt.
- Eine deterministische Sortierung, ein konfigurierbares Batchlimit und eine
  maximale Gesamtlaufzeit begrenzen jeden Dispatcher-Lauf.
- Nicht bearbeitete Restmengen bleiben fällig und werden beim nächsten Lauf
  berücksichtigt.
- Ein atomarer Claim pro Eintrag schützt zusätzlich zur globalen Sperre vor
  Doppelverarbeitung. Sperren werden in `finally` freigegeben; eine TTL fängt
  Prozessabbruch und verwaiste Sperren ab.
- Fehler-Backoff besitzt eine Obergrenze und wird nach Erfolg zurückgesetzt.

Bei PHP-CLI gilt empfohlen:

- Exit-Code `0`: Dispatcher technisch abgeschlossen; einzelne kontrollierte Fachfehler sind protokolliert.
- Exit-Code ungleich `0`: Dispatcher selbst konnte nicht sicher laufen, etwa bei Datenbankausfall.

Vorhandene allgemeine Projektkonventionen für Bootstrap, Logging und Fehlerausgabe
haben Vorrang.

---

# 10. Rechte, Sicherheit und Audit

Alle schreibenden Aktionen benötigen ein passendes Recht und CSRF-Schutz. Protokolliert werden unter anderem Änderungen von Einträgen und Standards, Aktivstatus, Verbindungstests, manuelle Abrufe und Cronläufe – jedoch nie Secret-Werte.

Verbindlich:

- serverseitige Validierung,
- parametrisierte Datenbankabfragen,
- Ausgabe-Escaping,
- keine dynamischen Includes oder Klassen aus Request-Werten,
- keine beliebigen Methodenaufrufe,
- SSRF-Schutz durch fest definierte API-Ziele,
- zentrale verschlüsselte Secret-Speicherung,
- leeres Secret-Feld beim Bearbeiten bedeutet „unverändert“,
- geschützter Cron-Aufruf,
- sichere Benutzermeldungen und interne Diagnose ohne Secrets.

---

# 11. Nicht erlaubt

- separate Open-Meteo-Verwaltung außerhalb des Integrationssystems,
- nur ein fest verdrahteter Eintrag pro Klasse,
- frei eingebbare PHP-Klassennamen,
- API-Aufrufe oder SQL in Navigationsdateien,
- weitere parallele Cronjob-Systeme neben dem in Folge 14 geschaffenen Einstieg,
- ungeschützte öffentliche Cron-URL,
- Secrets in HTML, Logs, Exceptions, URLs oder Cronzeilen,
- Überschreiben gültiger Wetterdaten durch Fehlerantworten,
- stiller Fallback auf eine nicht gewählte Standardintegration,
- ein einzelner Fehler, der den ganzen Dispatcher beendet.

---

# 12. Abnahmekriterien

1. Integrationen werden zentral im Einstellungsbereich verwaltet.
2. Klassen stammen aus einer sicheren Registry und erfüllen den Vertrag.
3. Deklarierte spezifische Fähigkeiten sind kontrolliert aufrufbar.
4. Mehrere Einträge derselben Klasse funktionieren unabhängig.
5. Einstellungen werden flexibel aus dem Klassenschema gerendert.
6. Je Fähigkeit kann ein konkreter globaler Standard gewählt werden.
7. Open-Meteo liefert normalisierte Wetterdaten für mehrere Standorte.
8. Nutzungsmodus, Kunden-API-Key und Attribution werden korrekt und sicher behandelt.
9. Letzte gültige Daten bleiben bei Fehlern erhalten.
10. Der in Folge 14 geschaffene zentrale Cronjob ruft fällige Integrationsjobs ohne Doppelverarbeitung auf.
11. Fehler werden je Eintrag isoliert und ohne Secret-Leaks protokolliert.
12. Rechte, CSRF, Validierung und SSRF-Schutz sind geprüft.
13. Der reale System-Cronjob wurde im Video eingerichtet und erfolgreich nachgewiesen.
