# 16_Tabellen_Standards.md

# Tabellen-Standards

**Version:** 1.0  
**Projekt:** NoobClaw Webinterface  
**Gültigkeit:** Verbindlich für alle Tabellen, Listenansichten, Datenraster, Logansichten und Verwaltungsübersichten

---

# 1. Zweck dieses Dokuments

Dieses Dokument definiert den verbindlichen Aufbau und das Verhalten sämtlicher Tabellen und Listenansichten der Anwendung.

Es beschreibt:

- Tabellenstruktur,
- Spaltenaufbau,
- Sortierung,
- Suche,
- Filter,
- Pagination,
- Mehrfachauswahl,
- Aktionen,
- Statusdarstellung,
- Responsive-Verhalten,
- Ladezustände,
- Empty States,
- Fehlerzustände,
- Barrierefreiheit,
- Performance,
- serverseitige Datenverarbeitung,
- Tabellen für Benutzer, Agenten, API, MCP und Logs.

Dieses Dokument ergänzt:

```text
13_Design_und_UI_Standards.md
14_CSS_Standards.md
15_Formular_Standards.md
```

---

# 2. Ziel

Tabellen sollen:

- große Datenmengen übersichtlich darstellen,
- schnell erfassbar sein,
- konsistent aufgebaut sein,
- serverseitig skalieren,
- auf Desktop, Tablet und Mobil funktionieren,
- vollständig per Tastatur bedienbar sein,
- Filter und Sortierungen nachvollziehbar machen,
- Aktionen sicher und eindeutig anbieten,
- technische und fachliche Informationen sauber trennen.

Eine Tabelle ist erst fertig, wenn folgende Zustände definiert sind:

- Daten werden geladen,
- Daten vorhanden,
- keine Daten vorhanden,
- Filter liefert keine Treffer,
- Fehler beim Laden,
- keine Berechtigung,
- teilweise Daten verfügbar,
- Auswahl aktiv,
- Massenaktion läuft.

---

# 3. Grundprinzipien

## 3.1 Tabellen nur für tabellarische Daten

Eine Tabelle wird nur verwendet, wenn die Daten aus klar vergleichbaren Zeilen und Spalten bestehen.

Geeignet:

```text
Benutzer
Agenten
API-Zugriffe
MCP-Zugriffe
Sessions
Logs
Einstellungen
```

Nicht geeignet:

```text
Komplexe Detailansichten
lange Beschreibungen
stark visuelle Inhalte
mehrstufige Prozesse
```

---

## 3.2 Eine Zeile entspricht einem Datensatz

Jede Tabellenzeile repräsentiert genau einen Datensatz oder ein eindeutig zusammengehöriges Ereignis.

Beispiele:

```text
ein Benutzer
ein Agent
ein API-Aufruf
ein MCP-Aufruf
eine Session
ein Logeintrag
```

---

## 3.3 Die wichtigste Information steht links

Die erste fachliche Spalte enthält die wichtigste Identifikation.

Beispiele:

```text
Benutzername
Agentenname
Request-ID
Einstellungs-Key
```

---

## 3.4 Aktionen stehen rechts

Zeilenbezogene Aktionen werden in der letzten Spalte dargestellt.

Beispiel:

```text
Öffnen
Bearbeiten
Deaktivieren
Löschen
Mehr
```

---

## 3.5 Weniger Spalten sind besser

Nicht jede Datenbankspalte gehört in die Übersicht.

Die Tabelle zeigt nur Informationen, die für:

- Identifikation,
- Vergleich,
- Entscheidung,
- Aktion

relevant sind.

Weitere Details werden in:

- Detailseite,
- Sidepanel,
- Dialog,
- ausklappbarer Zeile

angezeigt.

---

# 4. Standardaufbau einer Tabellenseite

```text
Seitenkopf
    ↓
Status- oder Informationsleiste
    ↓
Tabellen-Toolbar
    ↓
Aktive Filter
    ↓
Tabelle
    ↓
Pagination und Ergebnisanzahl
```

---

# 5. Aufbau einer Tabelle

```text
+---------------------------------------------------------------------+
| Toolbar                                                             |
+---------------------------------------------------------------------+
| Auswahl | Spalte 1 | Spalte 2 | Status | Datum | Aktionen          |
+---------------------------------------------------------------------+
| [ ]     | Wert      | Wert      | Aktiv | ...   | ...               |
| [ ]     | Wert      | Wert      | Fehler| ...   | ...               |
+---------------------------------------------------------------------+
| 1–25 von 843 Datensätzen                       < 1 2 3 ... 34 >      |
+---------------------------------------------------------------------+
```

---

# 6. Tabellencontainer

Jede Tabelle befindet sich in einem klar definierten Container.

Der Container kann enthalten:

- Toolbar,
- Tabellenkopf,
- Tabellenkörper,
- Ladezustand,
- Empty State,
- Fehlerzustand,
- Pagination.

Empfohlene Struktur:

```html
<section class="datentabelle">
    <header class="datentabelle__toolbar">
        ...
    </header>

    <div class="datentabelle__scrollbereich">
        <table class="tabelle">
            ...
        </table>
    </div>

    <footer class="datentabelle__footer">
        ...
    </footer>
</section>
```

---

# 7. Tabellenkopf

Der Tabellenkopf enthält:

- verständliche Spaltennamen,
- optionale Sortierfunktion,
- optionale Hilfetexte,
- keine internen Datenbanknamen.

Gut:

```text
Benutzer
E-Mail-Adresse
Status
Letzte Anmeldung
Aktionen
```

Schlecht:

```text
benutzer_id
email
aktiv
letzte_anmeldung
action
```

---

# 8. Spaltenreihenfolge

Empfohlene Reihenfolge:

```text
1. Auswahl
2. Identifikation
3. wichtige fachliche Daten
4. Zuordnung
5. Status
6. Zeitangaben
7. Aktionen
```

Beispiel Benutzer:

```text
Auswahl
Benutzername
Name
E-Mail-Adresse
Gruppen
Status
Letzte Anmeldung
Aktionen
```

---

# 9. Spaltenbreiten

Spalten erhalten sinnvolle Mindest- und Maximalbreiten.

Beispiele:

```text
Auswahl: 48px
Status: 100–140px
Datum: 140–180px
Aktionen: 80–140px
Bezeichnung: flexibel
Beschreibung: flexibel, aber begrenzt
```

Keine Spalte wird unnötig breit.

---

# 10. Textausrichtung

Verbindliche Regeln:

```text
Text: links
Zahlen: rechts
Status: links oder zentriert
Checkboxen: zentriert
Aktionen: rechts
```

Lange technische IDs können linksbündig dargestellt werden.

---

# 11. Zeilenhöhe

Standard:

```text
48px bis 56px
```

Kompakte Tabellen:

```text
40px bis 44px
```

Sehr hohe Zeilen werden vermieden.

Mehrzeilige Inhalte gehören bevorzugt in Detailansichten.

---

# 12. Tabellen-Dichte

Unterstützte Dichten:

```text
Komfortabel
Standard
Kompakt
```

Standard ist:

```text
Standard
```

Logs können standardmäßig kompakter dargestellt werden.

Formularähnliche Tabellen verwenden mindestens Standarddichte.

---

# 13. Hover-Zustand

Zeilen dürfen bei Hover dezent hervorgehoben werden.

Der Hoverzustand darf nicht mit dem Auswahlzustand verwechselt werden.

Beispiel:

```text
Hover: leicht veränderter Hintergrund
Ausgewählt: Primärfarbe mit klarer Markierung
```

---

# 14. Aktive und ausgewählte Zeilen

## 14.1 Aktive Zeile

Eine aktive Zeile ist der aktuell geöffnete oder fokussierte Datensatz.

## 14.2 Ausgewählte Zeile

Eine ausgewählte Zeile ist Bestandteil einer Mehrfachauswahl.

Beide Zustände müssen unterschiedlich dargestellt werden.

---

# 15. Zeilenaktionen

## 15.1 Direkte Aktionen

Maximal zwei häufige Aktionen werden direkt dargestellt.

Beispiele:

```text
Öffnen
Bearbeiten
```

## 15.2 Aktionsmenü

Weitere Aktionen befinden sich in einem Menü:

```text
Mehr
```

Beispiele:

```text
Duplizieren
Deaktivieren
Session beenden
Schlüssel erneuern
Löschen
```

---

# 16. Gefährliche Aktionen

Gefährliche Aktionen:

- stehen nicht direkt neben der Primäraktion ohne Abstand,
- verwenden die Fehlerfarbe,
- benötigen gegebenenfalls Bestätigung,
- dürfen nicht unbeabsichtigt ausgelöst werden.

Beispiele:

```text
Löschen
Agent stoppen
Session beenden
API-Schlüssel widerrufen
```

---

# 17. Klickbare Zeilen

Eine ganze Zeile darf klickbar sein, wenn sie zu einer Detailansicht führt.

Regeln:

- sichtbarer Hoverzustand,
- Tastaturfokus,
- eindeutiger Cursor,
- interne Buttons bleiben separat bedienbar,
- keine überraschende Aktion.

---

# 18. Sortierung

## 18.1 Sortierbare Spalten

Sortierung wird nur für sinnvolle Spalten angeboten.

Beispiele:

```text
Benutzername
Status
Erstellt am
Letzte Aktivität
Laufzeit
```

Nicht sinnvoll:

```text
Aktionsspalte
komplexe JSON-Felder
lange Beschreibungen
```

---

## 18.2 Sortierzustand

Der aktuelle Sortierzustand muss sichtbar sein.

Beispiele:

```text
aufsteigend
absteigend
nicht sortiert
```

Die Sortierung wird zusätzlich in der URL oder im Tabellenzustand gespeichert.

---

## 18.3 Standardsortierung

Jede Tabelle benötigt eine definierte Standardsortierung.

Beispiele:

```text
Benutzer: Benutzername aufsteigend
Logs: erstellt_am absteigend
Agenten: Haupt-Agent, dann Bezeichnung
Sessions: letzte_aktivitaet absteigend
```

---

# 19. Suche

## 19.1 Lokale Tabellensuche

Die Suche steht in der Toolbar.

Beispiele:

```text
Benutzer durchsuchen
Agenten durchsuchen
Logs durchsuchen
```

---

## 19.2 Suchverhalten

- Suche serverseitig bei größeren Datenmengen.
- Eingabe wird verzögert verarbeitet.
- Suche kann mit Enter bestätigt werden.
- Suchbegriff bleibt nach Neuladen erhalten.
- Ein Löschen-Button setzt die Suche zurück.

---

## 19.3 Durchsuchte Felder

Die durchsuchten Felder werden fachlich definiert.

Beispiel Benutzer:

```text
benutzername
vorname
nachname
anzeigename
email
```

Beispiel Agenten:

```text
bezeichnung
hostname
ip_adresse
beschreibung
```

---

# 20. Filter

## 20.1 Häufige Filter

Häufige Filter stehen direkt sichtbar in der Toolbar.

Beispiele:

```text
Status
Benutzergruppe
Agententyp
Quelle
Zeitraum
Erfolg
```

---

## 20.2 Erweiterte Filter

Seltene Filter befinden sich in:

```text
Weitere Filter
```

oder einem Sidepanel.

---

## 20.3 Aktive Filter

Aktive Filter werden als Chips dargestellt.

Beispiel:

```text
Status: Aktiv ×
Gruppe: Administrator ×
```

Zusätzlich existiert:

```text
Alle Filter zurücksetzen
```

---

# 21. Pagination

## 21.1 Serverseitige Pagination

Bei wachsenden Tabellen wird grundsätzlich serverseitig paginiert.

Nicht alle Datensätze werden vollständig in den Browser geladen.

---

## 21.2 Standardseitengröße

Empfohlen:

```text
25
50
100
```

Standard:

```text
25
```

---

## 21.3 Anzeige

Beispiel:

```text
1–25 von 843 Datensätzen
```

---

## 21.4 Seitennavigation

Enthält:

```text
Erste Seite
Vorherige Seite
Seitennummern
Nächste Seite
Letzte Seite
```

Bei vielen Seiten werden nicht alle Nummern angezeigt.

---

# 22. URL-Zustand

Folgende Tabellenzustände sollen in der URL abbildbar sein:

```text
seite
limit
suche
sortierung
richtung
filter
```

Beispiel:

```text
index.php?seite=settings_benutzer&suche=mario&status=aktiv&sortierung=benutzername&richtung=asc
```

Dadurch sind Ansichten:

- teilbar,
- bookmarkbar,
- nach Neuladen wiederherstellbar.

---

# 23. Mehrfachauswahl

## 23.1 Checkboxen

Die erste Spalte enthält Auswahlcheckboxen.

Der Tabellenkopf enthält:

```text
Alle sichtbaren auswählen
```

---

## 23.2 Auswahlstatus

Nach Auswahl erscheint eine Massenaktionsleiste.

Beispiel:

```text
5 Datensätze ausgewählt

[Deaktivieren] [Gruppe zuordnen] [Auswahl aufheben]
```

---

## 23.3 Auswahl über mehrere Seiten

Falls Auswahl über mehrere Seiten unterstützt wird, muss dies ausdrücklich angezeigt werden.

Beispiel:

```text
Alle 25 Datensätze dieser Seite sind ausgewählt.
Alle 843 Treffer auswählen?
```

---

# 24. Massenaktionen

Geeignete Massenaktionen:

```text
Aktivieren
Deaktivieren
Benutzergruppe zuordnen
Exportieren
Sessions beenden
Status ändern
```

Gefährliche Massenaktionen benötigen eine Bestätigung.

---

# 25. Inline-Bearbeitung

Inline-Bearbeitung wird sparsam verwendet.

Geeignet:

```text
Priorität
Aktivstatus
Bezeichnung
```

Nicht geeignet:

```text
Passwörter
API-Schlüssel
MCP-Schlüssel
komplexe Berechtigungen
JSON
```

---

# 26. Ausklappbare Zeilen

Ausklappbare Zeilen können Zusatzinformationen anzeigen.

Beispiele:

```text
Sub-Agenten
Request-Parameter
Logdetails
Sessioninformationen
```

Regeln:

- Zusatzinhalt bleibt übersichtlich,
- nicht mehrere Ebenen tief verschachteln,
- Zustand ist per Tastatur steuerbar.

---

# 27. Gruppierte Tabellen

Daten dürfen gruppiert dargestellt werden.

Beispiel Agenten:

```text
OpenClaw Server
    Coding Agent
    Recherche Agent

Hermes Server
    Support Agent
```

Gruppierung muss visuell klar sein.

---

# 28. Baumansichten

Baumtabellen werden nur eingesetzt, wenn echte Hierarchien bestehen.

Beispiel:

```text
Agent-Server
└── Sub-Agenten
```

Die Hierarchie muss auch ohne Farbe verständlich sein.

---

# 29. Statusdarstellung

Statuswerte werden als Badges oder klarer Text dargestellt.

Beispiele:

```text
Aktiv
Inaktiv
Verbunden
Getrennt
Fehler
Gesperrt
Abgelaufen
```

Statusfarben folgen:

```text
13_Design_und_UI_Standards.md
```

---

# 30. Datums- und Zeitwerte

Datenbankwerte sind Unix-Timestamps.

In der Benutzeroberfläche werden sie lokal formatiert.

Beispiel:

```text
16.07.2026, 13:45
```

Optional kann ein relativer Wert ergänzt werden:

```text
vor 5 Minuten
```

Der Wert `0` wird dargestellt als:

```text
–
```

oder:

```text
Nicht gesetzt
```

---

# 31. Zahlen und Einheiten

Zahlen werden formatiert.

Beispiele:

```text
1.250
12,5 ms
2,4 GB
843 Anfragen
```

Einheiten stehen immer konsistent.

---

# 32. IDs

Interne IDs werden nur angezeigt, wenn sie für die technische Arbeit relevant sind.

Technische IDs verwenden Monospace-Schrift.

Beispiele:

```text
Request-ID
Session-ID
Schlüssel-ID
Agenten-ID
```

---

# 33. Lange Texte

Lange Texte werden:

- gekürzt,
- mit Ellipse dargestellt,
- vollständig per Tooltip, Sidepanel oder Detailansicht zugänglich gemacht.

Wichtige Informationen dürfen nicht ausschließlich abgeschnitten werden.

---

# 34. JSON und technische Inhalte

JSON wird nicht unformatiert in einer normalen Tabellenzelle angezeigt.

Stattdessen:

```text
Details
JSON anzeigen
Parameter öffnen
```

Detaildarstellung:

- Monospace,
- formatierter Inhalt,
- Kopieren-Aktion,
- sensible Werte maskiert.

---

# 35. Ladezustand

Während des Ladens wird ein Tabellen-Skeleton angezeigt.

Der Aufbau soll die spätere Tabelle nachbilden.

Nicht erlaubt:

```text
leere weiße Fläche
```

oder nur ein großer Spinner ohne Kontext.

---

# 36. Empty State

Wenn noch keine Datensätze existieren:

```text
Noch keine Benutzer vorhanden

Lege den ersten Benutzer an.

[Benutzer anlegen]
```

---

# 37. Keine Treffer

Wenn Filter oder Suche keine Treffer liefern:

```text
Keine passenden Ergebnisse

Ändere die Suche oder setze die Filter zurück.

[Filter zurücksetzen]
```

Dieser Zustand unterscheidet sich vom vollständig leeren Datenbestand.

---

# 38. Fehlerzustand

Bei einem Ladefehler:

```text
Daten konnten nicht geladen werden

Bitte versuche es erneut.

[Erneut laden]
```

Technische Details gehören in Logs.

---

# 39. Teilfehler

Falls nur einzelne Inhalte nicht geladen werden können, bleibt die restliche Tabelle sichtbar.

Beispiel:

```text
Der Agentenstatus konnte nicht aktualisiert werden.
```

---

# 40. Responsive Verhalten

## 40.1 Desktop

- vollständige Tabelle,
- alle wichtigen Spalten sichtbar,
- Toolbar horizontal,
- fixe oder sticky Tabellenköpfe möglich.

## 40.2 Tablet

- unwichtige Spalten ausblenden,
- Toolbar umbrechen,
- horizontales Scrollen zulassen,
- Aktionen kompakter darstellen.

## 40.3 Mobil

Mögliche Strategien:

```text
horizontales Scrollen
Kartenansicht
priorisierte Spalten
Detailansicht pro Zeile
```

Die gewählte Strategie wird pro Tabelle bewusst festgelegt.

---

# 41. Sticky Header

Bei langen Tabellen kann der Tabellenkopf sticky sein.

Regeln:

- Hintergrund bleibt undurchsichtig,
- Z-Index ist kontrolliert,
- Kopf überdeckt keine Toolbar,
- Spalten bleiben ausgerichtet.

---

# 42. Sticky Spalten

Die erste oder letzte Spalte kann sticky sein, wenn dies für die Bedienung notwendig ist.

Beispiele:

```text
Benutzername
Aktionen
```

Sticky Spalten werden sparsam verwendet.

---

# 43. Horizontales Scrollen

Bei zu breiten Tabellen:

- horizontaler Scrollbereich,
- keine komplette Seitenverschiebung,
- wichtige Spalten können sticky bleiben,
- Scrollbarkeit muss erkennbar sein.

---

# 44. Barrierefreiheit

## 44.1 Semantik

Echte Tabellen verwenden:

```html
<table>
<thead>
<tbody>
<tr>
<th>
<td>
```

Keine Tabellen-Nachbildung nur mit `div`, wenn echte tabellarische Daten vorliegen.

---

## 44.2 Spaltenüberschriften

```html
<th scope="col">
```

Zeilenüberschriften bei Bedarf:

```html
<th scope="row">
```

---

## 44.3 Sortierung

Sortierzustand wird mit `aria-sort` abgebildet.

Beispiel:

```html
<th scope="col" aria-sort="ascending">
```

---

## 44.4 Auswahl

Checkboxen besitzen eindeutige Labels.

Beispiel:

```text
Benutzer Mario auswählen
```

---

## 44.5 Tastatur

Alle Aktionen müssen per Tastatur erreichbar sein.

Unterstützt:

```text
Tab
Shift + Tab
Enter
Space
Escape
Pfeiltasten in Menüs
```

---

# 45. Serverseitige Verarbeitung

Verbindlicher Ablauf:

```text
Request empfangen
    ↓
Berechtigung prüfen
    ↓
Suchparameter normalisieren
    ↓
Filter validieren
    ↓
Sortierung erlauben
    ↓
Limit begrenzen
    ↓
Daten laden
    ↓
Gesamtzahl laden
    ↓
Ergebnis ausgeben
```

---

# 46. Erlaubte Sortierfelder

Sortierfelder werden über eine Whitelist definiert.

Nicht erlaubt:

Benutzerwerte direkt in `ORDER BY` einsetzen.

Beispiel:

```php
$erlaubte_sortierungen = [
    'benutzername',
    'email',
    'aktiv',
    'erstellt_am',
];
```

---

# 47. Limit und Offset

Pagination verwendet:

```text
LIMIT
OFFSET
```

oder ein geeignetes Cursor-Verfahren bei sehr großen Tabellen.

Das maximale Limit wird serverseitig begrenzt.

---

# 48. Suchabfragen

Suche verwendet vorbereitete Statements.

Benutzereingaben werden niemals direkt in SQL eingebaut.

---

# 49. Zentrale Funktionen

Für jede Fachobjekt-Tabelle existiert eine zentrale Funktionsdatei.

Beispiel:

```text
benutzer
    → funktionen_benutzer.php

agents
    → funktionen_agents.php

api_log
    → funktionen_api_log.php

mcp_log
    → funktionen_mcp_log.php
```

Typische Funktionen:

```php
benutzer_array()
benutzer_anzahl()
benutzer_laden()

agents_array()
agents_anzahl()

api_log_array()
api_log_anzahl()
```

---

# 50. Standardparameter für Array-Funktionen

Empfohlener Aufbau:

```php
benutzer_array(
    array $filter = [],
    string $suche = '',
    string $sortierung = 'benutzername',
    string $richtung = 'asc',
    int $limit = 25,
    int $offset = 0
);
```

Zugehörig:

```php
benutzer_anzahl(
    array $filter = [],
    string $suche = ''
);
```

---

# 51. Strukturierte Rückgabe

Empfohlen:

```php
[
    'erfolg' => true,
    'daten' => [...],
    'gesamt' => 843,
    'seite' => 1,
    'limit' => 25,
    'sortierung' => 'benutzername',
    'richtung' => 'asc',
    'filter' => [...],
]
```

---

# 52. Export

Mögliche Exportformate:

```text
CSV
JSON
```

Optional später:

```text
XLSX
PDF
```

Regeln:

- Export berücksichtigt aktive Filter,
- Export protokollieren,
- Berechtigungen prüfen,
- große Exporte asynchron verarbeiten,
- sensible Spalten ausschließen.

---

# 53. Tabellenkonfiguration

Benutzerspezifische Tabellenoptionen können später über die Tabelle `einstellungen` gespeichert werden.

Beispiele:

```text
sichtbare Spalten
Spaltenreihenfolge
Seitengröße
Dichte
Standardsortierung
gespeicherte Filter
```

Speicherung über:

```text
funktionen_einstellungen.php
```

---

# 54. Gespeicherte Ansichten

Später mögliche Ansichten:

```text
Meine aktiven Agenten
Fehlgeschlagene API-Aufrufe
Gesperrte Benutzer
MCP-Fehler der letzten 24 Stunden
```

Eine gespeicherte Ansicht enthält:

```text
Filter
Sortierung
sichtbare Spalten
Seitengröße
```

---

# 55. Benutzer-Tabelle

Empfohlene Spalten:

```text
Auswahl
Benutzername
Name
E-Mail-Adresse
Gruppen
Status
Letzte Anmeldung
Aktionen
```

---

# 56. Benutzer-Login-Tabelle

Empfohlene Spalten:

```text
Zeit
Benutzername
Benutzer
Erfolg
Fehlergrund
IP-Adresse
Session
Aktionen
```

Standardsortierung:

```text
erstellt_am absteigend
```

---

# 57. Sessions-Tabelle

Empfohlene Spalten:

```text
Benutzer
IP-Adresse
Browser / Gerät
Erstellt
Letzte Aktivität
Gültig bis
Status
Aktionen
```

Aktionen:

```text
Details
Session beenden
```

---

# 58. API-Benutzer-Tabelle

Empfohlene Spalten:

```text
Benutzername
Bezeichnung
Schlüssel-ID
Status
Gültig bis
Letzte Verwendung
Letzte IP-Adresse
Aktionen
```

Der geheime Schlüssel wird niemals angezeigt.

---

# 59. API-Log-Tabelle

Empfohlene Spalten:

```text
Zeit
API-Benutzer
Request-ID
Methode
Modul
Aktion
HTTP-Status
Erfolg
Laufzeit
Aktionen
```

---

# 60. MCP-Benutzer-Tabelle

Empfohlene Spalten:

```text
Benutzername
Bezeichnung
Schlüssel-ID
Tools
Resources
Status
Gültig bis
Letzte Verwendung
Aktionen
```

---

# 61. MCP-Log-Tabelle

Empfohlene Spalten:

```text
Zeit
MCP-Benutzer
Request-ID
Methode
Tool / Resource
Erfolg
Client
Laufzeit
Aktionen
```

---

# 62. Agenten-Tabelle

Empfohlene Spalten:

```text
Agent
Typ
Haupt-Agent
Hostname / IP
Status
Sub-Agenten
Letzte Aktivität
Aktionen
```

Haupt-Agenten und Sub-Agenten werden visuell unterscheidbar dargestellt.

---

# 63. Agenten-Log-Tabelle

Empfohlene Spalten:

```text
Zeit
Agent
Quelle
Benutzer
Aktion
Status
Laufzeit
Nachricht
Aktionen
```

---

# 64. Datenbankfehler-Tabelle

Empfohlene Spalten:

```text
Zeit
Quelle
SQL-Code
Datei
Funktion
Request-ID
Kurzmeldung
Aktionen
```

Vollständige SQL-Befehle werden nur mit entsprechender Berechtigung dargestellt.

Geheimnisse müssen maskiert sein.

---

# 65. System-Log-Tabelle

Empfohlene Spalten:

```text
Zeit
Stufe
Bereich
Quelle
Meldung
Benutzer / Agent
Request-ID
Aktionen
```

---

# 66. Einstellungen-Tabelle

Eine direkte technische Tabellenansicht der Einstellungen ist nur für Administratoren vorgesehen.

Empfohlene Spalten:

```text
Bereich
Key
Wert
Datentyp
Beschreibung
Geändert
Aktionen
```

Lange Werte werden gekürzt.

JSON wird in der Detailansicht formatiert.

---

# 67. CSS-Klassen

Empfohlene Klassen:

```text
.datentabelle
.datentabelle__toolbar
.datentabelle__scrollbereich
.datentabelle__footer

.tabelle
.tabelle__kopf
.tabelle__zeile
.tabelle__zelle
.tabelle__aktionen
.tabelle__auswahl

.tabelle--kompakt
.tabelle--gestreift
.tabelle--interaktiv

.ist-sortiert
.ist-ausgewaehlt
.ist-aktiv
.ist-geladen
.ist-fehlerhaft
```

---

# 68. HTML-Beispiel

```html
<section class="datentabelle">
    <header class="datentabelle__toolbar">
        <div class="toolbar__suche">
            <label class="sr-only" for="benutzer-suche">
                Benutzer durchsuchen
            </label>

            <input
                class="eingabe"
                id="benutzer-suche"
                name="suche"
                type="search"
                placeholder="Benutzer durchsuchen"
            >
        </div>

        <div class="toolbar__aktionen">
            <a
                class="button button--primaer"
                href="index.php?seite=settings_benutzer_bearbeiten"
            >
                Benutzer anlegen
            </a>
        </div>
    </header>

    <div class="datentabelle__scrollbereich">
        <table class="tabelle">
            <thead>
                <tr>
                    <th scope="col">
                        <span class="sr-only">Auswahl</span>
                    </th>

                    <th scope="col" aria-sort="ascending">
                        Benutzername
                    </th>

                    <th scope="col">E-Mail-Adresse</th>
                    <th scope="col">Status</th>
                    <th scope="col">Letzte Anmeldung</th>

                    <th scope="col">
                        <span class="sr-only">Aktionen</span>
                    </th>
                </tr>
            </thead>

            <tbody>
                <tr>
                    <td>
                        <input
                            type="checkbox"
                            aria-label="Benutzer Mario auswählen"
                        >
                    </td>

                    <th scope="row">
                        Mario
                    </th>

                    <td>mario@example.de</td>

                    <td>
                        <span class="status-badge status-badge--erfolg">
                            Aktiv
                        </span>
                    </td>

                    <td>16.07.2026, 13:45</td>

                    <td class="tabelle__aktionen">
                        <a
                            class="button button--sekundaer button--klein"
                            href="index.php?seite=settings_benutzer&id=15"
                        >
                            Öffnen
                        </a>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>

    <footer class="datentabelle__footer">
        <p>1–25 von 843 Datensätzen</p>

        <nav aria-label="Seitennavigation">
            ...
        </nav>
    </footer>
</section>
```

---

# 69. JavaScript-Regeln

JavaScript darf:

- Sortierung auslösen,
- Filter bedienen,
- Massenaktionen steuern,
- Sidepanels öffnen,
- Auswahlstatus verwalten,
- Spalten ein- und ausblenden,
- Tabellenzustände speichern,
- Daten asynchron nachladen.

JavaScript darf nicht:

- Berechtigungen ersetzen,
- Daten ungeprüft löschen,
- serverseitige Pagination umgehen,
- SQL-nahe Parameter ungeprüft übergeben,
- Sicherheitsentscheidungen treffen.

---

# 70. Logging

Folgende Tabellenaktionen werden bei Bedarf protokolliert:

```text
Export
Massenänderung
Löschen
Deaktivieren
Session beenden
Schlüssel widerrufen
Agent stoppen
Spaltenkonfiguration speichern
```

Reine Sortier- und Filteraktionen müssen nicht zwingend als Audit-Log gespeichert werden.

---

# 71. Performance

## 71.1 Verbindliche Regeln

- Serverseitige Pagination verwenden.
- Nur benötigte Spalten laden.
- Indizes berücksichtigen.
- Keine `SELECT *` bei großen Tabellen.
- Gesamtanzahl effizient ermitteln.
- Suche auf sinnvolle Felder beschränken.
- Große JSON-Felder nicht in Übersichten laden.
- Details erst bei Bedarf laden.
- Statusabfragen bündeln.
- Keine API- oder MCP-Einzelabfrage pro Tabellenzeile.

---

## 71.2 N+1-Abfragen vermeiden

Schlecht:

```text
25 Agenten laden
für jeden Agenten einzeln den Status laden
```

Besser:

```text
Agenten und benötigte Statusdaten gesammelt laden
```

---

# 72. Berechtigungen

Tabellen prüfen mindestens:

```text
anzeigen
details_anzeigen
bearbeiten
loeschen
exportieren
massenaktionen
```

Nicht erlaubte Aktionen werden nicht angezeigt.

Die serverseitige Prüfung bleibt trotzdem verbindlich.

---

# 73. Qualitätsprüfung

Eine Tabelle gilt erst als fertig, wenn folgende Punkte geprüft wurden:

```text
[ ] Tabelle ist für die Datenart geeignet
[ ] Wichtigste Information steht links
[ ] Aktionen stehen rechts
[ ] Standardsortierung definiert
[ ] Erlaubte Sortierfelder als Whitelist
[ ] Suche definiert
[ ] Filter definiert
[ ] Serverseitige Pagination vorhanden
[ ] Ergebnisanzahl vorhanden
[ ] Ladezustand vorhanden
[ ] Empty State vorhanden
[ ] Keine-Treffer-Zustand vorhanden
[ ] Fehlerzustand vorhanden
[ ] Mehrfachauswahl sinnvoll geprüft
[ ] Gefährliche Aktionen bestätigt
[ ] Responsive Darstellung geprüft
[ ] Tastaturbedienung geprüft
[ ] Tabellen-Semantik korrekt
[ ] Light Mode geprüft
[ ] Dark Mode geprüft
[ ] Keine sensiblen Daten sichtbar
[ ] Keine N+1-Abfragen
[ ] Keine direkten SQL-Zugriffe in der Seite
[ ] Rechte serverseitig geprüft
[ ] URL-Zustand geprüft
[ ] Export berücksichtigt Filter
```

---

# 74. Verbotene Muster

Nicht erlaubt:

```text
Alle Datensätze ohne Pagination laden
SELECT * für große Übersichten
Sortierfelder ungeprüft aus dem Request übernehmen
Sensible Schlüssel anzeigen
JSON unformatiert in Tabellenzellen ausgeben
Nur Farbe zur Statusdarstellung verwenden
Aktionen ohne Berechtigungsprüfung anzeigen
Löschen ohne Bestätigung
Leere Tabelle ohne Empty State
Technische Fehlermeldungen im Frontend
Horizontales Scrollen der gesamten Seite
Doppelte Tabellenkomponenten
Clientseitige Filterung großer Datenmengen
Eine API-Abfrage pro Zeile
```

---

# 75. Zusammenfassung

Alle Tabellen und Listenansichten der Anwendung folgen einem einheitlichen Standard.

Verbindliche Grundsätze:

- klare Spaltenhierarchie,
- wichtigste Informationen links,
- Aktionen rechts,
- serverseitige Suche, Filterung, Sortierung und Pagination,
- sichere Whitelists für Sortierung,
- verständliche Statusanzeigen,
- vollständige Lade-, Leer- und Fehlerzustände,
- responsive Darstellung,
- semantische HTML-Tabellen,
- vollständige Tastaturbedienung,
- keine sensiblen Inhalte,
- keine direkten SQL-Zugriffe,
- zentrale Funktionen pro Fachobjekt,
- skalierbare Datenabfragen,
- Vermeidung von N+1-Abfragen.

Neue Tabellenansichten erweitern das bestehende Tabellensystem.

Sie dürfen keine abweichende Tabellenlogik oder eigene Designsprache einführen.