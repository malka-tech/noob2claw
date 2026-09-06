# Noob2Claw – Folge 20: Startprompt
# Kanban-Board, Agenten-Trigger und allgemeiner Mastercronjob

Du arbeitest als Entwickler am bestehenden Projekt Noob2Claw. Implementiere einen
eigenen Kanban-Bereich und erweitere den vorhandenen zentralen Cron-Einstieg zu
einem allgemeinen Mastercronjob.

# 1. Grundlagen

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

Lies die Architektur- und UI-Vorgaben aus Folge 9, die Agenten- und
Auftragsverwaltung aus Folge 10, die Cron-Architektur und Einrichtungsanleitung
aus Folge 14 sowie:

- `docs/folge_20_kanban/1_Kanban_und_Agenten_Trigger.md`,
- `docs/folge_20_kanban/2_Mastercronjob_Einrichtung.md`.

Erhalte lokale Benutzeränderungen. Analysiere vor der Umsetzung den realen Code,
das Schema, die vorhandene Agentenkommunikation, Rechte, Navigation, API, MCP,
Migrationen, Benachrichtigungen und `cron.php`. Erweitere zentrale Funktionen;
baue keine parallele Rechte-, Nachrichten-, Job-, Logging- oder Cronarchitektur.

# 2. Pflichtumfang

Implementiere:

1. eigenen Navigationsbereich `Kanban`,
2. mehrere Boards mit Mitgliedern und Sichtbarkeit,
3. sortierbare Spalten und Karten,
4. Titel, Beschreibung, Priorität, Fälligkeit, Labels und Checklisten,
5. Zuständigkeiten für Benutzer und registrierte Agenten,
6. Kommentare, Anhänge über die vorhandene Dateiverwaltung und Aktivitätsverlauf,
7. sichere Drag-and-drop-Verschiebung mit Konflikterkennung,
8. Archive statt unkontrollierter Löschung,
9. konfigurierbaren Agenten-Trigger alle X Minuten,
10. idempotente Erinnerungszustellung über die vorhandene Agentenkommunikation,
11. Triggerläufe, Zustellstatus, Backoff und Audit,
12. allgemeinen Masterdispatcher über den vorhandenen CLI-Einstieg `cron.php`,
13. Umstellung des realen System-Cronjobs von `integrationen` auf `alle`,
14. Rechte, CSRF-Schutz, Validierung, Migrationen und vollständige Tests.

# 3. Kanban-Bereich

Der Navigationspunkt wird nach dem bestehenden automatischen Navigationskonzept
registriert. Navigationsdateien enthalten kein SQL und keine Business-Logik.

Mindestens erforderlich:

- Boardübersicht mit Suche, eigenen Boards und archivierten Boards,
- Boardansicht mit horizontalen Spalten,
- Karten erstellen, öffnen, bearbeiten, verschieben und archivieren,
- responsive Alternative zu Drag-and-drop für Tastatur und Mobilgeräte,
- Filter nach Zuständigkeit, Agent, Priorität, Label, Frist und Status,
- klare Lade-, Leer-, Fehler- und Konfliktzustände.

Alle Lese- und Schreibaktionen prüfen Anmeldung, allgemeines Recht,
Boardmitgliedschaft, konkrete Objektberechtigung und validierte Aktion
serverseitig. Eine sichtbare Karte oder Navigation ersetzt keine Autorisierung.

# 4. Agenten-Zuständigkeit

Eine Karte kann keinem, einem oder mehreren Benutzern beziehungsweise Agenten
zugeordnet werden. Agentenreferenzen zeigen ausschließlich auf vorhandene aktive
Agenten aus der zentralen Agentenverwaltung. Inaktive oder gelöschte Agenten
werden nachvollziehbar gekennzeichnet und nicht still ersetzt.

Ein Agent erhält nur die für die konkrete Karte freigegebenen Informationen. Die
Kartenbeschreibung und Kommentare sind nicht vertrauenswürdige Eingaben und
dürfen Systemprompt, Toolrechte, Dateirechte oder Sicherheitsgrenzen nicht
überschreiben.

# 5. Agenten-Trigger alle X Minuten

Trigger werden pro Board oder Agentenzuordnung konfiguriert. Mindestens:

```text
aktiv
intervall_minuten
naechster_lauf_am
letzter_versuch_am
letzter_erfolg_am
fehler_anzahl
letzter_status
```

Das Intervall besitzt ein sicheres Minimum, standardmäßig 15 Minuten, und eine
konfigurierbare Obergrenze. Der Trigger sendet keine pauschale Erinnerung für das
gesamte Board. Er bündelt ausschließlich relevante, offene und für den Agenten
autorisierte Karten, etwa neue Zuweisungen, fällige oder seit der letzten
erfolgreichen Zustellung geänderte Karten.

Jede Zustellung besitzt einen stabilen Idempotenzschlüssel aus Trigger,
Agent, Zeitfenster und Kartenstand. Atomare Claims mit TTL verhindern doppelte
Nachrichten bei parallelen oder wiederholten Cronläufen. Leere Erinnerungen
werden nicht versendet. Fehler verwenden begrenzten Backoff; ein Agentenfehler
blockiert andere Agenten nicht.

Verwende die bestehende Agenten-Auftragsverwaltung und deren Aktion
`kanban_trigger`. Der Trigger erzeugt einen normalen, atomar abholbaren
Agentenauftrag; `cron.php` startet den Agenten nicht direkt. Kein direkter
unvalidierter HTTP-Aufruf aus dem Kanban-Modul, kein zweites Auftrags- oder
Nachrichtensystem und keine Geheimnisse in Auftrag, Cronzeile oder Protokoll.

# 6. Allgemeiner Mastercronjob

Prüfergebnis: `/var/www/noobclaw/cron.php` ist bereits der zentrale und richtige
CLI-Einstieg. Der Aufruf `cron.php integrationen` reicht für Folge 20 nicht aus,
weil er nur Integrationen verarbeitet.

Erweitere die feste serverseitige Aufgabenregistry mindestens um:

```text
integrationen
kanban_erinnerungen
alle
```

`cron.php alle` startet einen Masterdispatcher. Dieser ruft alle registrierten
und aktivierten internen Dispatcher in deterministischer Reihenfolge auf. Jeder
Teildispatcher besitzt eigenen Fehlerkontext, Zeitlimit, Batchlimit, atomare
Sperre und strukturiertes Laufprotokoll. Ein fachlicher Fehler in Kanban stoppt
nicht die Integrationen und umgekehrt. Schlägt Bootstrap oder Masterdispatcher
selbst fehl, endet der Prozess mit Exit-Code ungleich `0`.

`cron.php integrationen` und `cron.php kanban_erinnerungen` bleiben für gezielte
manuelle Tests verfügbar. HTTP-Aufrufe und unbekannte Schlüssel werden weiterhin
abgelehnt. `cron.php` enthält keine Fachlogik, sondern nur CLI-Prüfung, Bootstrap,
Registryauflösung und Dispatch.

Es bleibt bei genau einer System-Cronzeile:

```cron
* * * * * /usr/bin/php /var/www/noobclaw/cron.php alle
```

Entferne die bisherige Zeile `cron.php integrationen`, nachdem `alle` manuell
erfolgreich geprüft wurde. Beide Zeilen dürfen nicht parallel aktiv bleiben.
Richte den realen Cronjob nach `2_Mastercronjob_Einrichtung.md` ein und weise
automatische Läufe für Integrationen und Kanban nach.

# 7. Sicherheit und Daten

Migrationen sind idempotent, nicht destruktiv und verwenden sichere Standardwerte.
Nutze Transaktionen für Sortierung, Verschiebung, Claims und Zustellstatus.
Verbindlich sind parametrisierte SQL-Abfragen, Ausgabe-Escaping, CSRF-Schutz,
serverseitige Validierung, sichere Anhänge, Rate-Limits und Auditierung.

Mindestrechte:

```text
kanban_anzeigen
kanban_boards_erstellen
kanban_boards_verwalten
kanban_karten_erstellen
kanban_karten_bearbeiten
kanban_karten_verschieben
kanban_karten_archivieren
kanban_kommentieren
kanban_agenten_zuordnen
kanban_trigger_verwalten
kanban_verlauf_anzeigen
kanban_mastercron_anzeigen
```

Administratoren erhalten neue Rechte initial; andere Rollen nicht automatisch.

# 8. Tests

Teste mindestens:

- Navigation, alle Rechte und negative Direktzugriffe,
- zwei Boards mit unterschiedlichen Mitgliedern,
- Kartenanlage, Bearbeitung, Archivierung und Wiederherstellung,
- Drag-and-drop, Tastatur-/Mobilalternative und parallele Verschiebungen,
- Sortierung ohne doppelte oder verlorene Positionen,
- Benutzer- und Agentenzuordnung einschließlich inaktivem Agenten,
- Kommentare, Checklisten, Fristen, Labels und geschützte Anhänge,
- Trigger aktiv/inaktiv, Intervallgrenzen und Fälligkeit,
- Bündelung ohne leere oder doppelte Erinnerungen,
- atomare Claims, TTL, Fehlerisolierung, Backoff und Wiederaufnahme,
- Agent sieht nur berechtigte Karten und keine fremden Boarddaten,
- Prompt-Injection in Karte oder Kommentar erweitert keine Agentenrechte,
- `cron.php integrationen`, `cron.php kanban_erinnerungen` und `cron.php alle`,
- unbekannte Aufgabe und HTTP-Aufruf werden abgelehnt,
- paralleler Masterlauf wird sicher verhindert,
- ein fehlerhafter Teildispatcher blockiert den anderen nicht,
- nur eine aktive System-Cronzeile,
- echter automatischer Lauf beider Teildispatcher,
- PHP-Syntax, Migrationen, Browserdarstellung und bestehende Regressionstests.

# 9. Abschlussbericht

Dokumentiere Architektur, Datenmodell, Dateien, Migrationen, Rechte, Board- und
Kartenfunktionen, Agentenzustellung, Idempotenz, Sperren, Backoff, Masterregistry,
finale Cronzeile, Tests, Ergebnisse und bekannte Einschränkungen.

Die Folge ist erst abgeschlossen, wenn Kanban und Agenten-Trigger funktionieren,
`cron.php alle` Integrationen und Kanban fehlerisoliert verarbeitet, genau eine
allgemeine Cronzeile real eingerichtet ist und ein automatischer Lauf beider
Bereiche nachgewiesen wurde.
