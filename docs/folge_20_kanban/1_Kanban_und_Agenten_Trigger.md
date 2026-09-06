# Noob2Claw – Folge 20: Kanban und Agenten-Trigger

# 1. Fachliches Modell

```text
Board
├── Mitglieder und Rechte
├── Triggerkonfigurationen
└── Spalten
    └── Karten
        ├── Zuständigkeiten
        ├── Labels
        ├── Checkliste
        ├── Kommentare
        ├── Anhänge
        └── Aktivitätsverlauf
```

Boards sind voneinander getrennte Berechtigungsräume. Jede Objektabfrage prüft
das Board und die Mitgliedschaft, nicht nur eine übergebene Karten-ID.

# 2. Datenmodell

Mindestens benötigte Strukturen:

```text
kanban_boards
kanban_board_mitglieder
kanban_spalten
kanban_karten
kanban_karten_zustaendige
kanban_labels
kanban_karten_labels
kanban_checklistenpunkte
kanban_kommentare
kanban_anhaenge
kanban_aktivitaeten
kanban_trigger
kanban_trigger_laeufe
kanban_trigger_zustellungen
mastercron_laeufe
mastercron_teillaeufe
```

Boards, Spalten und Karten besitzen stabile IDs, Ersteller, Änderungszeit,
Archivstatus und eine Versionsnummer für Konflikterkennung. Sortierpositionen
werden serverseitig validiert und innerhalb einer Transaktion konsistent neu
vergeben. Harte Löschung ist nur nach bestehendem Datenschutz-/Löschkonzept
zulässig.

Karten enthalten mindestens Titel, bereinigte Beschreibung, Priorität, Start-
und Fälligkeitszeit, optionale Abschlusszeit, Spalten-ID und Position. Datenbank-
zeiten werden in UTC gespeichert und in der Benutzerzeitzone dargestellt.

# 3. Oberfläche

Der eigene Hauptbereich `Kanban` nutzt die bestehenden Designkomponenten.

Boardübersicht:

- eigene, geteilte und archivierte Boards,
- Suche und sichere Filter,
- letzter Änderungszeitpunkt und Anzahl offener Karten,
- klarer Leer-, Lade- und Fehlerzustand.

Boardansicht:

- horizontal scrollbare Spalten,
- kompakte Karten mit Titel, Priorität, Frist und Zuständigen,
- Filter ohne Verlust nicht gespeicherter Eingaben,
- Detaildialog oder Detailseite für alle Kartendaten,
- Drag-and-drop sowie Buttons/Tastaturaktion für Verschieben,
- Live-Konflikthinweis statt stiller Überschreibung.

Die Oberfläche darf optimistisch reagieren, bestätigt eine Verschiebung aber erst
nach erfolgreicher serverseitiger Prüfung. Bei Konflikt wird der aktuelle Stand
neu geladen und verständlich angezeigt.

# 4. Rechte und Objektzugriff

Eine Aktion benötigt:

```text
angemeldete Identität
→ allgemeines Kanban-Recht
→ Boardmitgliedschaft oder Verwaltungsrecht
→ objektspezifische Berechtigung
→ validierte Aktion
```

Boardrollen können `betrachter`, `bearbeiter` und `verantwortlicher` umfassen.
Die zentrale Rechteverwaltung bleibt maßgeblich. API und MCP prüfen dieselben
Fachfunktionen wie die Weboberfläche.

# 5. Agenten-Erinnerung

Der Trigger erinnert einen konkreten Agenten an relevante Arbeit. Eine
Erinnerung enthält nur:

- Board- und Kartenreferenz,
- Titel und freigegebene Kurzbeschreibung,
- Spalte, Priorität und Frist,
- erlaubte nächste Aktionen,
- Link oder stabile interne Objektkennung.

Nicht enthalten sind fremde Karten, Secrets, private Kommentare ohne Freigabe,
ungeprüfte Anhänge oder technische Systemanweisungen.

Relevanzregeln sind serverseitig begrenzt und nachvollziehbar, zum Beispiel:

- seit letzter erfolgreicher Zustellung neu zugewiesen,
- inhaltlich geändert,
- Frist nähert sich oder ist überschritten,
- ausdrücklich als „Agent erinnern“ markiert,
- noch nicht abgeschlossen oder archiviert.

Ein Reminder ist Information beziehungsweise Arbeitsimpuls. Er erteilt keine
zusätzlichen Tool- oder Objektrechte. Der Agent muss jede spätere Aktion über die
normalen API-/MCP-Rechte ausführen.

# 6. Triggerzustand und Idempotenz

Ein fälliger Trigger wird atomar beansprucht. Der Lauf lädt eine begrenzte Menge
relevanter Karten, prüft die Berechtigung erneut, erzeugt eine normalisierte
Aufgabe und legt sie über die bestehende Agenten-Auftragsverwaltung als Aktion
`kanban_trigger` an. Der zuständige Agenten-Client holt diesen Auftrag über den
vorhandenen atomaren Auftragsablauf ab. Der Cronprozess führt keinen Agenten
direkt aus.

Eine Zustellung speichert mindestens:

```text
trigger_id
agent_id
karten_id
karten_version
zeitfenster
idempotenzschluessel
status
versuche
agentenauftrag_id_optional
erstellt_am
zugestellt_am_optional
sicherer_fehler_optional
```

Die Zustellung verweist zusätzlich auf die zentrale ID des erzeugten
Agentenauftrags. Dessen vorhandene Status- und Abhollogik bleibt maßgeblich.

Der Idempotenzschlüssel besitzt einen Unique-Index. Ein Timeout mit unbekanntem
Zustellstatus wird vor einer Wiederholung abgeglichen; er beweist nicht, dass
keine Nachricht zugestellt wurde.

Nach Erfolg wird der nächste Termin stabil vom vorherigen Solltermin aus
berechnet. Verpasste Intervalle werden nicht als Erinnerungslawine nachgeholt.
Fehler nutzen begrenzten exponentiellen Backoff. Batchlimit, maximale Laufzeit
und maximale Nachrichtenmenge schützen System und Agenten vor Überlastung.

# 7. Masterdispatcher

Der Masterdispatcher ist eine Registry erlaubter interner Aufgaben:

```text
integrationen       → bestehender Integrations-Dispatcher
kanban_erinnerungen → fällige Kanban-Trigger
alle                → alle aktivierten Teildispatcher
```

`alle` bedeutet nicht, beliebige PHP-Funktionen dynamisch aufzurufen. Die Registry
ist fest serverseitig definiert. Jeder Teillauf liefert normiert Status, Dauer,
Zähler und sicheren Fehler. Der Master protokolliert Gesamtlauf und Teilläufe.

Parallelität:

- globale Mastersperre gegen zwei gleichzeitige `alle`-Läufe,
- eigene Sperre je Teildispatcher,
- atomarer Claim je Trigger,
- Unique-Idempotenzschlüssel je Zustellung,
- Freigabe in `finally` und TTL für verwaiste Claims.

Gezielte manuelle Aufrufe dürfen einen gerade laufenden Teil nicht doppelt
starten. Die Sperrstrategie muss `alle` und Einzelaufrufe gemeinsam abdecken.

# 8. Sicherheit

- parametrisierte SQL-Abfragen und zentrale Fachfunktionen,
- CSRF-Schutz bei jeder schreibenden Webaktion,
- serverseitige Rechte- und Objektprüfung bei jeder API-/MCP-Aktion,
- Ausgabe-Escaping für Titel, Beschreibungen, Labels und Kommentare,
- sichere vorhandene Upload-/Downloadfunktion für Anhänge,
- keine HTML-, SQL-, Shell- oder Toolausführung aus Karteninhalten,
- keine Secrets in Erinnerungen, Cronzeile oder Protokollen,
- Größenlimits für Text, Checklisten, Kommentare, Anhänge und Boardumfang,
- Rate-Limits für Änderungen und manuelle Trigger,
- Audit für Board-, Rechte-, Karten-, Trigger- und Zustelländerungen.

# 9. Abnahmekriterien

1. Der Bereich `Kanban` erscheint automatisch und nur mit passendem Recht.
2. Mehrere Boards sind durch Mitgliedschaft und Objektrechte getrennt.
3. Spalten und Karten lassen sich sicher und konfliktfest sortieren.
4. Benutzer und aktive Agenten können nachvollziehbar zugeordnet werden.
5. Triggerintervalle sind begrenzt, fälligkeitsstabil und deaktivierbar.
6. Erinnerungen enthalten nur autorisierte relevante Karten.
7. Doppelte Cronläufe erzeugen keine doppelten Erinnerungen.
8. Prompt-Inhalte einer Karte erweitern keine Agentenrechte.
9. Der Masterdispatcher verarbeitet Integrationen und Kanban fehlerisoliert.
10. Genau eine allgemeine System-Cronzeile ist aktiv und nachgewiesen.
