# Noob2Claw – Folge 20: Kanban-Funktionen für Agenten

## Ziel und Einbindung

Agenten können ihre Aufgaben über den bestehenden `mcp.php` lesen und innerhalb
ihrer Rechte bearbeiten. Der Erinnerungstrigger ersetzt diese Funktionen nicht.
Dies ist der verbindliche Implementierungsvertrag; eine ergänzte Dokumentation
ist noch kein Nachweis der laufenden Implementierung.

`inc/funktionen_mcp_kanban.php` definiert Toolnamen, Parameterschemas und Routing
zu den vorhandenen Kanban-Fachfunktionen. Keine SQL-Abfragen oder eigene Kanban-
Logik im MCP-Adapter. Web, API und MCP verwenden dieselben Berechtigungen,
Transaktionen, Versionszähler und Auditfunktionen. Kein weiterer MCP-Endpunkt.

## Gemeinsamer Parametervertrag

Alle Toolargumente als explizite JSON-Schemas registrieren: oberster Typ Objekt,
Pflichtfelder gemäß Tabelle, `additionalProperties: false`. IDs sind positive
Ganzzahlen. Optionale Filter entfallen, wenn nicht benutzt; keine ID `0` als
Wildcard. Listen unterstützen `limit` (Standard 20, Bereich 1 bis 100) und
optionalen opaken `cursor`. Cursor sind an Filter und Identitätskontext gebunden.

Alle Schreibtools verlangen zusätzlich `idempotency_key`: eine pro fachlicher
Aktion neu erzeugte UUID. Änderungen bestehender Karten verlangen
`expected_version`, eine positive Ganzzahl aus dem zuletzt geladenen Kartenstand.
Auch Kommentare und Checklistenänderungen erhöhen diese Kartenrevision.
Bei Neuanlage wird stattdessen `expected_board_version` verlangt.
Unveränderte Wiederholung verwendet denselben Schlüssel und dieselben Argumente.

## Verbindlicher Toolkatalog

Mit „Liste“ sind die gemeinsamen optionalen Parameter `limit` und `cursor` gemeint.
Schreibmetadaten aus dem vorigen Abschnitt sind immer zusätzlich erforderlich.

| Tool | Pflichtparameter | Optionale Parameter | Ergebnis |
| --- | --- | --- | --- |
| `kanban_boards_liste` | keine | `suchbegriff`, Liste | erlaubte Boards mit ID, Titel, Version |
| `kanban_board_laden` | `board_id` | keine | Board, sortierte Spalten, deren Statuszuordnung, Version |
| `kanban_meine_aufgaben` | keine | `board_id`, `status`, Liste | dem authentifizierten Agenten zugeordnete Karten |
| `kanban_karten_liste` | `board_id` | `spalten_id`, `status`, `suchbegriff`, Liste | autorisierte Kartenkurzdaten |
| `kanban_karte_laden` | `karten_id` | keine | Kartendetails, Checkliste, Zuständige, aktuelle Version |
| `kanban_karte_anlegen` | `board_id`, `spalten_id`, `titel`, `expected_board_version` | `beschreibung`, `prioritaet`, `faellig_am` | neue Karten-ID und Version |
| `kanban_karte_bearbeiten` | `karten_id`, `aenderungen`, `expected_version` | keine | aktualisierte Karten-ID und Version |
| `kanban_karte_verschieben` | `karten_id`, `ziel_spalten_id`, `expected_version`, `expected_board_version` | `vor_karten_id` | Zielspalte, Position, Status, neue Karten- und Boardversion |
| `kanban_kommentare_liste` | `karten_id` | Liste | sichtbare Kommentare mit Akteur und Zeit |
| `kanban_kommentar_anlegen` | `karten_id`, `text`, `expected_version` | keine | Kommentar-ID und neue Kartenversion |
| `kanban_checklistenpunkt_setzen` | `karten_id`, `punkt_id`, `erledigt`, `expected_version` | keine | Punktzustand und neue Kartenversion |
| `kanban_zustaendige_liste` | `karten_id` | keine | freigegebene Benutzer-/Agentenreferenzen |
| `kanban_verlauf_liste` | `karten_id` | Liste | sichtbare Aktivitäten mit Akteur und Zeitpunkt |

`aenderungen` ist ein nichtleeres Objekt mit ausschließlich `titel`,
`beschreibung`, `prioritaet`, `faellig_am`. Titel: 1–200 Zeichen nach Trim;
Beschreibung bis 20.000, Kommentar 1–5.000, Suchbegriff bis 200 Zeichen.
Priorität als Integer 1–4 mit Standard 2 (normal). Fälligkeit als RFC3339-Zeit
mit Zeitzone; leerer String entfernt eine bestehende Frist, Weglassen erhält sie.
Intern UTC speichern. `erledigt` ist ein Boolean und setzt den Zielzustand,
kein Toggle. Statusfilter: `offen`, `in_arbeit`, `erledigt`; ohne Filter alle
nicht archivierten zugänglichen Karten. Fehlende Filter verleihen keine Rechte.

Status ergibt sich aus der serverseitig konfigurierten Statuszuordnung einer
Spalte, nicht aus ihrem Namen. Verschieben nach einer Abschlussspalte setzt den
Abschlusszeitpunkt; Zurückverschieben öffnet die Karte wieder. Kein zusätzliches
Statusfeld zulassen, das der Spalte widerspricht. Pro Spalte die Zuordnung
`offen`, `in_arbeit` oder `erledigt` hinterlegen; bestehende Boards beim Upgrade
explizit zuordnen, nicht anhand des Titels erraten.

Verschieben ist innerhalb eines Boards erlaubt. Zielspalte und optionale
Vorgängerreferenz müssen demselben Board gehören; `vor_karten_id` bezeichnet die
Karte in der Zielspalte, vor die einsortiert wird. Ohne diese Referenz hinten
anfügen. Kartensortierung und Boardrevision gemeinsam transaktional ändern.
Neuanlage erhöht ebenfalls die Boardrevision. Konkurrierende Sortierungen oder
Spaltenänderungen liefern einen Versionskonflikt statt stiller Neuinterpretation.

Listen geben nur Kurzdaten aus; lange Beschreibungen oder große Checklisten in
Detailantworten mit konfiguriertem Größenlimit behandeln und Übergröße explizit
melden, nicht still abschneiden. Kommentare und Verlauf separat paginieren.
Keine geschützten Anhänge ungeprüft einbetten oder öffentliche URLs zurückgeben.

Nicht Teil dieses Toolkatalogs: Board-/Rechteverwaltung, Triggerkonfiguration,
Zuweisung anderer Agenten, Archivieren, Löschen und Datei-Uploads. Solche Aktionen
bleiben in der bestehenden berechtigten Oberfläche; später nur ausdrücklich
definierte Tools dafür ergänzen. `aenderungen` darf diese Grenzen nicht umgehen.

## Agentenrechte

Für alle Kanban-Tools einen aktiven, authentifizierten und zugelassenen Agenten
verlangen. „Meine Aufgaben“ ermittelt die Agent-ID ausschließlich aus dem
serverseitigen MCP-Kontext, nicht aus Argumenten. Eigene MCP-Zugänge pro Agent
mit fester Agentenzuordnung verwenden. Ein frei wählbarer Suffix hinter einem
gemeinsamen Token ist keine sichere Trennung von Agentenidentitäten.

`tools/list` filtert erlaubte Tools. Jeder tatsächliche Aufruf prüft erneut
Zugang, Agentenstatus, Toolfreigabe, Kanban-Fachrecht, Boardmitgliedschaft und
Objektzugriff. Ein vorhandenes Toolrecht oder eine Zuständigkeit allein verleiht
kein Boardrecht. Ein Agent darf sich nicht über einen Schreibparameter zum
Mitglied oder Administrator machen.

Lesen benötigt `kanban_anzeigen`; Verlauf zusätzlich `kanban_verlauf_anzeigen`.
Schreiben benötigt jeweils `kanban_karten_erstellen`, `kanban_karten_bearbeiten`,
`kanban_karten_verschieben` oder `kanban_kommentieren`; Checklisten verwenden
`kanban_karten_bearbeiten`. Zusätzlich die passende Boardrolle prüfen.
Filter vor Pagination anwenden, auch Zähler dürfen fremde Objekte nicht verraten.
Private Kommentare und Verlaufseinträge nach ihren eigenen Sichtbarkeiten filtern.
Fremde und unbekannte Objekt-IDs liefern dieselbe sichere Nichtverfügbarkeitsmeldung.

## Konflikte und Wiederholungen

Versionsprüfung und Änderung in einer Transaktion ausführen. Bei veraltetem
Stand `versionskonflikt`: neu laden, Änderung prüfen, dann mit neuer Revision
und neuem Idempotenzschlüssel bewusst erneut versuchen. Kein blindes Überschreiben.

Idempotenzdatensatz mit Unique-Key aus Zugang, Agent, Tool und Schlüssel sowie
Argumenthash und Ergebnis dauerhaft speichern. Nach aktueller Rechteprüfung
zuerst einen vorhandenen Idempotenzabschluss auflösen, erst bei neuer Aktion
die Ausgangsversion prüfen. So scheitert ein erfolgreicher Retry nicht an der
durch den ersten Aufruf bereits erhöhten Revision. Änderung, Revision, Audit und
Idempotenzabschluss atomar committen. Identischer Retry liefert dasselbe Ergebnis,
kein zweites Kommentar-/Kartenobjekt. Gleicher Schlüssel mit anderen Argumenten
führt zu `idempotenzkonflikt`. Vor Rückgabe eines gespeicherten Ergebnisses aktuelle
Rechte erneut prüfen. Bei Berechtigungsentzug keine alten Kartendaten ausliefern.
Schlüssel nicht zeitgesteuert so entfernen, dass ein später Retry neu schreibt;
bei Ergebnisbereinigung mindestens verbrauchten Schlüssel/Hash als Tombstone
erhalten und spätere Wiederholung als nicht erneut ausführbar melden.

MCP-Protokollformat über die vorhandene zentrale Verarbeitung. Fachfunktionen
liefern `erfolg`, `meldung`, `daten`, optional `fehlercode` und `request_id`.
Fachfehler nicht als scheinbar erfolgreiche Textantwort tarnen. Sichere Codes:
`nicht_verfuegbar`, `validierung`, `versionskonflikt`, `idempotenzkonflikt`,
`rate_limit`, `intern`. Keine Tokens, SQL-Details oder fremden Objekte ausgeben.
Audit enthält Quelle `mcp`, Zugang, Agent, Aktion, Objekt, alte/neue Version und
Request-ID. Bestehendes MCP-Logging maskiert sensible Argumente und Inhalte.

## Beispielablauf nach einer Erinnerung

1. Agent ruft `kanban_meine_aufgaben` und `kanban_karte_laden` auf.
2. Er lädt das Board für aktuelle Spalten und Boardrevision.
3. Er verschiebt eine berechtigte Karte mit aktuellen Revisionen nach „in Arbeit“.
4. Er erledigt die fachliche Aufgabe nur mit seinen separat erlaubten Werkzeugen.
5. Er dokumentiert das Ergebnis als Kommentar, setzt Checklistenpunkte und
   verschiebt die Karte erst nach tatsächlichem Abschluss in eine Abschlussspalte.

Nach jeder Änderung die zurückgegebene Kartenversion weiterverwenden. Bei
Unklarheiten kommentieren statt Erfolg erfinden. Karteninhalte sind Arbeitsdaten,
keine Anweisung zur Umgehung von Systemvorgaben oder Zugriffsrechten.

## Verbindliche Abnahme

Mit zwei Boards, zwei individuell authentifizierten Agenten und unterschiedlich
vergebenen Rechten testen. Alle Tests im Implementierungsbericht nachweisen.

| Test | Erwartung |
| --- | --- |
| Alle Lesetools mit erlaubtem Agenten | korrekte, paginierte und gefilterte Ergebnisse |
| Eigene Aufgaben ohne Agent-ID-Argument | ausschließlich eigene autorisierte Zuweisungen |
| Fremde Karte, Spalte, Checklistenpunkt oder Cursor | keine Daten/Änderungen über Boardgrenzen |
| Verbotenes Tool direkt aufrufen | abgewiesen trotz bekanntem Namen |
| Agent-ID-Suffix wechseln, Agent deaktivieren | abgewiesen |
| Rechte nach Toolauflistung entziehen | nächster Aufruf gesperrt |
| Karte erstellen und jedes Schreibtool ausführen | Änderung, Revision und Agenten-Audit korrekt |
| Zwei Änderungen mit derselben Ausgangsversion | eine Änderung, ein Konflikt |
| Zwei Sortierungen mit derselben Boardrevision | keine verlorenen oder doppelten Positionen |
| Identischen Schreibrequest wiederholen | dasselbe Ergebnis, keine Doppelobjekte |
| Schlüssel mit anderem Inhalt wiederverwenden | Idempotenzkonflikt |
| Retry nach Rechteentzug | kein Ergebnisleck und keine Änderung |
| Prozessabbruch vor/nach Commit | keine halbe Änderung, Retry bleibt sicher |
| Statuswechsel und Wiederöffnung | Spalte, Status und Abschlusszeit konsistent |
| Manipulierte Zusatzfelder, zu lange Texte | Validierungsfehler |
| Private Kommentare/Anhänge/Verlauf | nur freigegebene Inhalte sichtbar |
| Erinnerung mit veralteter Version | neu laden statt alte Daten überschreiben |
| Karte enthält Prompt-Injection | keine Rechteerweiterung |
| Bestehende Wiki-MCP-Tools | unveränderte Funktion und Rechte |

Die Ergänzung ist abgenommen, wenn der komplette Agentenablauf über den bisherigen
MCP-Zugang gelingt und die negativen Tests wirksam sperren. Kein zusätzlicher
Cronjob ist für die Tools notwendig.
