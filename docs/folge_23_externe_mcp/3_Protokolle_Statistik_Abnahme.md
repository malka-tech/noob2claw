# Folge 23 – Protokolle, Statistik und Abnahme

## Gesendete und empfangene Daten nachvollziehen

Jeder Aufruf erhält eine Request-ID. Detailansicht mit Zeitpunkt, Agent,
MCP-Zugang, Server, lokalem/externalem Toolnamen, genehmigter Katalogversion,
Status, Dauer und zwei Bereichen: **Gesendet** und **Empfangen**.
Filter nach Zeitraum, Server, Agent, Funktion und Fehler; paginierte Anzeige.
Abgelehnte Aufrufe zeigen „nicht gesendet“, nicht eine erfundene ERP-Antwort.

Es gibt drei Logmodi:

- Metadaten: Standard, Inhalte als „nicht aufgezeichnet“ gekennzeichnet.
- Maskierte Inhalte: ausdrücklich pro Server/Tool aktivieren; JSON-Pfade für
  erlaubte Datenfelder konfigurieren, alles andere ausblenden.
- Zeitlich begrenzte Diagnose: zusätzliche geprüfte Felder für Testdaten,
  automatisch ablaufend. Niemals unmaskierte Tokens oder Passwörter speichern.

So lassen sich tatsächliche Datenflüsse prüfen, ohne vollständige Kundendatensätze
dauerhaft mitzuschreiben. Maskierung erfolgt **vor** Datei- und DB-Schreiben.
Authorization, Cookies, API-Schlüssel und konfigurierte personenbezogene Felder
rekursiv entfernen. Unstrukturierte Textantworten standardmäßig nur als Typ,
Länge und gekürzten Hinweis protokollieren; eine Namens-Blacklist reicht dort
nicht aus. Binärdaten nicht als Base64 ins Log, nur Typ und Größe.
Kennzeichnen, ob Inhalt maskiert, abgeschnitten oder nicht erfasst wurde.

Geschützte JSONL-Logdatei außerhalb des Webroots, Pfad über Installationseinstellung,
nicht per Agent vorgebbar. Dienstbenutzer braucht Schreibrecht; bei Einrichtung
gezielt testen. Beispielname `mcp-extern-YYYY-MM-DD.jsonl`. JSON-kodierte einzelne
Events verhindern Zeileninjektion. Zugriff ausschließlich über die berechtigte
Backendansicht oder kontrollierten Export, niemals öffentliche Download-URL.
HTML escapen, CSV-Export gegen Tabellenformeln absichern.

Pro logischem Aufruf ein Start- und ein Abschlussereignis, einzelne Versuche mit
Versuchsnummer. Verwaiste Starts nach Prozessabbruch als unbekannt abgleichen.
Zuerst dauerhaftes Auditereignis erfassen, dann senden. Bei nicht verfügbarer
Auditpersistenz keine neuen externen Aufrufe ausführen. Scheitert Protokollierung
nach Versand, Alarm und unklaren Auditstatus erfassen, keinesfalls Aufruf wiederholen.

Vorgeschlagene Betriebswerte: Rotation täglich oder ab 50 MiB, maskierte Inhalte
7 Tage, Metadaten 30 Tage, aggregierte Statistik 90 Tage. Werte administrativ
anpassen und Speicherbudget prüfen. Löschen erfasst Dateien, Index und Exporte;
Backupaufbewahrung gesondert dokumentieren. Payloadansicht und Export benötigen
zusätzliche Rechte; deren Zugriff selbst auditieren.

## Statistik

Dashboard mit Zeitfilter und Gruppierung nach Server, Tool und Agent:

- logische Aufrufe, tatsächliche Versandversuche und blockierte Versuche getrennt;
- Erfolge, Toolfehler, Transportfehler, Timeouts und unbekannte Ausgänge;
- mittlere und p95-Laufzeit, gesendete/empfangene Bytes;
- meistgenutzte Funktionen und Fehlerrate pro Server;
- Katalog-Cachetreffer, Refreshfehler, Katalogalter und letzte erfolgreiche Verbindung.

Erfolg heißt gültiges Ergebnis ohne `isError`, nicht HTTP 200 allein. Für fachliche
ERP-Erfolgscodes gegebenenfalls zusätzliche, explizite Bewertung anzeigen.
Kennzahlen nach Request-ID deduplizieren. Retries sind zusätzliche Versuche, nicht
zusätzliche fachliche Aufrufe. Abgelehnte Aufrufe haben null Upstream-Versuche.
p95 aus Messwerten oder Histogrammen ermitteln, niemals aus Tagesdurchschnitten.
Zeitfenster intern UTC, Anzeige in Benutzerzeitzone. Leere Zeitfenster zeigen
„keine Daten“, nicht eine gemessene Laufzeit von null. Aggregation mit Cursor und
Idempotenz; Rohdaten erst nach erfolgreicher Aggregation gemäß Frist löschen.

## Tests für die Implementierung

Test-MCP-Server mit zwei lesenden Tools und einem schreibenden Sandbox-Tool.
Zwei Agenten mit unterschiedlichen eigenen Zugängen, zwei Server mit gleichem
Originaltoolnamen, verschiedene Präfixe. Keine echten ERP-Buchungen zum Testen.

| Testfall | Erwartung |
| --- | --- |
| Verbindung und mehrseitiger Katalog | vollständiger atomarer Snapshot |
| Präfixe `erp` und `lager`, gleiches Originaltool | eindeutige Namen, richtiges Ziel |
| Agent A sieht nur Suche; Agent B auch Anlegen | Listen entsprechend gefiltert |
| A ruft verborgenes Schreibtool direkt auf | abgewiesen, kein Upstream-Aufruf |
| Agent-ID-Suffix tauschen | abgewiesen, eigene Identität unverändert |
| Recht entziehen bei warmem Cache | nächster Aufruf sofort abgewiesen |
| Neue Funktion nach Refresh | ohne manuelle Freigabe unsichtbar |
| Beschreibung/Schema ändern | betroffene Funktion bis Freigabe gesperrt |
| Entferntes Tool, erfolgreich leere Liste | Tool beziehungsweise alle Tools gesperrt |
| Fehler auf Seite 2 oder Cursor-Schleife | kein partieller Snapshot aktiviert |
| Viele gleichzeitige Katalogabfragen | kein Live-Discovery-Sturm |
| Katalog älter als maximales Alter | keine externen Aufrufe |
| Tokenrotation oder 401 | alte Session/Katalog nicht weiter nutzbar |
| JSON- und SSE-Antworten | korrekte Zuordnung und Protokollverarbeitung |
| Strukturiertes Ergebnis und `isError` | Inhalt erhalten, Status korrekt |
| Nicht unterstützte Pflichtfähigkeit | klare Ablehnung, keine Teilimplementierung |
| Schreib-Timeout nach Versand | unbekannter Ausgang, kein Auto-Retry |
| Zwei Agenten parallel | keine Session-/Ergebnisvermischung |
| Bestätigung mit anderen Argumenten wiederverwenden | abgewiesen |
| Manipulierte URL, Redirect, DNS-Wechsel | unerlaubtes Ziel nicht kontaktiert |
| Geheimnisse/HTML/Zeilenumbrüche in Antwort | keine Lecks, XSS oder Log-Injektion |
| Loglaufwerk voll | keine unprotokollierten neuen Aufrufe |
| Neustart bei offenem Aufruf | unklarer Ausgang sichtbar, kein doppeltes Schreiben |
| Statistik mehrfach aggregieren | identische Zähler statt Doppelzählung |
| Retention und Logexport | Fristen/Rechte wirksam, keine öffentlichen Dateien |
| Native Wiki-Tools aus Folge 13 | unverändert verfügbar nach bisherigen Rechten |

Abnahmebericht enthält Softwarestand, Version des Testservers, ausgeführte Tests,
Ergebnisse und offene Einschränkungen. Die Tests dieser Konzeptfolge sind noch
nicht ausgeführt. Erst die implementierte und getestete Funktion ist betriebsbereit.

## Einfacher Demonstrationsablauf

Server mit Präfix `erp` anlegen, Katalog laden, einem Testagenten nur Kundensuche
geben. Über den bestehenden Noob2Claw-Zugang suchen. Danach zeigen: gesperrter
Schreibaufruf, maskierte Anfrage/Antwort, Statistik und unveränderte Toolliste
ohne erneute externe Discovery. Anschließend ein Recht entziehen und die sofortige
Sperre demonstrieren. Ausschließlich fiktive Daten verwenden.
