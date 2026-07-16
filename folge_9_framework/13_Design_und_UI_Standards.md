# 13_Design_und_UI_Standards.md

# Design- und UI-Standards

**Version:** 1.0  
**Projekt:** NoobClaw Webinterface  
**Gültigkeit:** Verbindlich für alle bestehenden und zukünftigen Seiten, Module und Komponenten

---

# 1. Zweck dieses Dokuments

Dieses Dokument definiert das vollständige visuelle und funktionale Grundsystem der Benutzeroberfläche.

Es beschreibt:

- die visuelle Identität,
- den Aufbau der Anwendung,
- die Designprinzipien,
- die Layoutregeln,
- die wiederverwendbaren UI-Komponenten,
- die Zustände einer Oberfläche,
- die Responsive-Logik,
- die Anforderungen an Barrierefreiheit,
- die Regeln für neue Module.

Dieses Dokument legt fest, **wie das System aussieht und wie es sich anfühlt**.

Detaillierte technische CSS-Regeln werden später in folgender Datei beschrieben:

```text
14_CSS_Standards.md
```

Formulare werden separat definiert in:

```text
15_Formular_Standards.md
```

Tabellen werden separat definiert in:

```text
16_Tabellen_Standards.md
```

---

# 2. Verbindlichkeit

Alle Entwickler und KI-Agenten müssen diese Datei vor der Erstellung oder Änderung einer Benutzeroberfläche lesen.

Neue Seiten dürfen:

- keine eigene Farbwelt einführen,
- keine neuen Abstände erfinden,
- keine eigenen Buttonstile definieren,
- keine abweichenden Kartenlayouts verwenden,
- keine eigenen Schriftarten verwenden,
- keine individuellen Navigationskonzepte umsetzen,
- keine vorhandenen Komponenten duplizieren.

Wenn eine benötigte Komponente noch nicht existiert, wird sie zuerst als allgemeine, wiederverwendbare Komponente definiert.

Erst danach darf sie innerhalb eines Moduls verwendet werden.

---

# 3. Designziel

Die Oberfläche soll wirken wie eine moderne, professionelle Agentic- und Enterprise-Plattform.

Sie soll gleichzeitig:

- technisch,
- modern,
- ruhig,
- hochwertig,
- übersichtlich,
- schnell erfassbar,
- klar strukturiert,
- produktiv nutzbar

wirken.

Das System ist keine verspielte Consumer-App.

Es ist eine professionelle Steuerungsoberfläche für:

- Benutzer,
- Systeme,
- KI-Agenten,
- MCP,
- APIs,
- Logs,
- Konfigurationen,
- Workflows,
- technische Prozesse.

---

# 4. Gestaltungsprinzipien

## 4.1 Klarheit vor Dekoration

Jedes visuelle Element benötigt einen funktionalen Zweck.

Dekoration darf niemals:

- Informationen überdecken,
- den Fokus stören,
- die Lesbarkeit verschlechtern,
- wichtige Aktionen verstecken.

---

## 4.2 Konsistenz vor Individualität

Gleiche Funktionen sehen immer gleich aus.

Beispiele:

- Speichern-Buttons sehen überall gleich aus.
- Löschen-Aktionen verwenden überall denselben Gefahrenstil.
- Statusanzeigen verwenden immer dieselben Farben.
- Seitenköpfe folgen immer demselben Aufbau.
- Formulare verwenden dieselben Feldgrößen und Abstände.
- Tabellen verwenden dieselbe Toolbar und Pagination.

---

## 4.3 Informationshierarchie

Jede Seite muss auf den ersten Blick beantworten:

1. Wo befinde ich mich?
2. Was ist der Zweck dieser Seite?
3. Welche Informationen sind wichtig?
4. Welche Aktion kann ich ausführen?
5. Gibt es Warnungen oder Fehler?

---

## 4.4 Weniger, aber besser

Die Oberfläche soll nicht mit Informationen überladen werden.

Stattdessen:

- klare Gruppen,
- logische Abschnitte,
- progressive Offenlegung,
- Details erst bei Bedarf,
- sinnvolle Standardansichten.

---

## 4.5 Produktivität vor Showeffekt

Animationen, Schatten und visuelle Effekte werden zurückhaltend eingesetzt.

Das System soll schnell und direkt wirken.

Keine unnötigen:

- Glaseffekte,
- Neonfarben,
- 3D-Effekte,
- Hintergrundanimationen,
- übertriebenen Übergänge,
- dekorativen Partikel.

---

# 5. Visuelle Identität

## 5.1 Grundstil

Der visuelle Stil ist:

```text
Modern
Technisch
Dunkel oder hell nutzbar
Minimalistisch
Hochwertig
Strukturiert
```

---

## 5.2 Formensprache

Die Oberfläche verwendet:

- leicht abgerundete Ecken,
- klare rechteckige Flächen,
- wenig sichtbare Rahmen,
- ruhige Schatten,
- präzise Abstände,
- eindeutige Statusfarben.

Empfohlene Radien:

```text
Kleine Elemente: 6px
Standardelemente: 8px
Buttons und Eingabefelder: 8px
Karten: 12px
Dialoge: 14px
Große Panels: 16px
```

Sehr stark abgerundete Pillenformen werden nur für Badges, Tags oder kompakte Statusanzeigen verwendet.

---

# 6. Farbkonzept

## 6.1 Primärfarbe

Die Primärfarbe kennzeichnet:

- Hauptaktionen,
- aktive Navigation,
- Links,
- Fokuszustände,
- ausgewählte Elemente,
- wichtige Hervorhebungen.

```text
Primär: #2563EB
Primär Hover: #1D4ED8
Primär Aktiv: #1E40AF
Primär Hell: #DBEAFE
```

---

## 6.2 Funktionsfarben

### Erfolg

```text
Basis: #16A34A
Hell: #DCFCE7
Dunkel: #166534
```

Verwendung:

- erfolgreich,
- aktiv,
- verbunden,
- abgeschlossen,
- bestätigt.

### Warnung

```text
Basis: #F59E0B
Hell: #FEF3C7
Dunkel: #92400E
```

Verwendung:

- Warnung,
- teilweise verfügbar,
- ausstehend,
- Aufmerksamkeit erforderlich.

### Fehler

```text
Basis: #DC2626
Hell: #FEE2E2
Dunkel: #991B1B
```

Verwendung:

- Fehler,
- kritisch,
- fehlgeschlagen,
- löschen,
- gesperrt.

### Information

```text
Basis: #0EA5E9
Hell: #E0F2FE
Dunkel: #075985
```

Verwendung:

- Hinweise,
- Informationen,
- neue Funktionen,
- neutrale Systemmeldungen.

---

## 6.3 Neutrale Farben – Light Mode

```text
Seitenhintergrund: #F8FAFC
Flächenhintergrund: #FFFFFF
Sekundäre Fläche: #F1F5F9
Hover-Fläche: #F8FAFC
Rahmen: #E2E8F0
Starker Rahmen: #CBD5E1
Haupttext: #0F172A
Sekundärtext: #475569
Deaktivierter Text: #94A3B8
```

---

## 6.4 Neutrale Farben – Dark Mode

```text
Seitenhintergrund: #0B1120
Flächenhintergrund: #111827
Sekundäre Fläche: #1E293B
Hover-Fläche: #172033
Rahmen: #273449
Starker Rahmen: #334155
Haupttext: #F8FAFC
Sekundärtext: #CBD5E1
Deaktivierter Text: #64748B
```

---

## 6.5 Farbregeln

Farben dürfen niemals nur dekorativ eingesetzt werden.

Jede Farbe benötigt eine eindeutige Bedeutung.

Nicht erlaubt:

- grün für Fehler,
- rot für neutrale Informationen,
- orange für erfolgreiche Aktionen,
- Primärblau für Löschaktionen.

Ein Status muss zusätzlich zur Farbe auch durch Text oder Icon erkennbar sein.

---

# 7. Typografie

## 7.1 Schriftfamilie

Primäre Schrift:

```text
Inter
```

Fallback:

```text
system-ui
-apple-system
BlinkMacSystemFont
"Segoe UI"
Arial
sans-serif
```

Falls Inter nicht lokal eingebunden wird, verwendet die Anwendung den System-Font-Stack.

---

## 7.2 Grundgrößen

```text
Seitentitel / H1: 30px
Bereichstitel / H2: 24px
Untertitel / H3: 20px
Komponententitel: 16px
Standardtext: 14px
Kleiner Text: 13px
Hilfstext: 12px
```

---

## 7.3 Schriftgewichte

```text
Normal: 400
Mittel: 500
Halbfett: 600
Fett: 700
```

Sehr schwere Schriftgewichte über `700` werden im Interface vermieden.

---

## 7.4 Zeilenhöhe

```text
Überschriften: 1.2 bis 1.3
Standardtext: 1.5
Lange Beschreibungen: 1.6
Tabellen: 1.4
```

---

## 7.5 Textfarben

- Hauptinformationen verwenden Haupttext.
- Erklärungen verwenden Sekundärtext.
- Platzhalter verwenden deaktivierten Text.
- Links verwenden die Primärfarbe.
- Fehlertexte verwenden die Fehlerfarbe.
- Erfolgstexte verwenden die Erfolgsfarbe.

---

## 7.6 Textregeln

Nicht erlaubt:

- ausschließlich Großbuchstaben für längere Texte,
- unnötig kleine Schrift unter 12px,
- mehrere Schriftarten,
- extrem lange Zeilen,
- unstrukturierte Textblöcke.

Empfohlene maximale Textbreite für längere Beschreibungen:

```text
680px
```

---

# 8. Abstände und Rhythmus

## 8.1 Grundraster

Alle Abstände orientieren sich an einem 4-Pixel-Raster.

Erlaubte Standardwerte:

```text
4px
8px
12px
16px
20px
24px
32px
40px
48px
64px
```

Beliebige Zwischenwerte werden vermieden.

---

## 8.2 Standardabstände

```text
Icon zu Text: 8px
Feldlabel zu Eingabefeld: 6px
Zwischen Formularfeldern: 16px
Zwischen Komponenten: 24px
Zwischen Seitenbereichen: 32px
Seitenrand Desktop: 32px
Seitenrand Tablet: 24px
Seitenrand Mobil: 16px
Karteninnenabstand: 20px bis 24px
Dialoginnenabstand: 24px
```

---

# 9. Grundlayout der Anwendung

## 9.1 Desktop-Aufbau

```text
+------------------------------------------------------------------+
| Globaler Header                                                  |
+----------------------+-------------------------------------------+
|                      | Seitenkopf                                |
| Sidebar              +-------------------------------------------+
|                      |                                           |
| Hauptnavigation      | Hauptinhalt                               |
|                      |                                           |
| Unterbereiche        |                                           |
|                      |                                           |
|                      |                                           |
+----------------------+-------------------------------------------+
```

---

## 9.2 Hauptbereiche

Die Anwendung besteht aus:

```text
Header
Sidebar
Seitenkopf
Inhaltsbereich
Optionaler rechter Detailbereich
Modale Ebenen
Toast-Ebene
```

---

## 9.3 Breiten

```text
Headerhöhe: 64px
Sidebar geöffnet: 256px
Sidebar eingeklappt: 72px
Maximale Inhaltsbreite bei Formularseiten: 1280px
Dashboard: volle verfügbare Breite
```

---

## 9.4 Scrollverhalten

- Der Header bleibt fixiert.
- Die Sidebar bleibt fixiert.
- Der Inhaltsbereich scrollt.
- Tabellen können innerhalb ihres Containers horizontal scrollen.
- Dialoge besitzen bei langen Inhalten einen eigenen Scrollbereich.
- Die komplette Seite darf keine unnötigen horizontalen Scrollbalken erzeugen.

---

# 10. Globaler Header

## 10.1 Inhalt

Der Header kann enthalten:

- Logo oder Produktname,
- globale Suche,
- Systemstatus,
- Benachrichtigungen,
- Theme-Umschalter,
- Benutzerprofil,
- Schnellaktionen.

---

## 10.2 Aufbau

```text
+------------------------------------------------------------------+
| Logo | Suche                             | Status | Theme | User |
+------------------------------------------------------------------+
```

---

## 10.3 Regeln

- Der Header ist ruhig und kompakt.
- Es werden maximal zwei primäre Schnellaktionen angezeigt.
- Benutzername und Avatar befinden sich rechts.
- Statusmeldungen werden nicht als dauerhaft blinkende Elemente dargestellt.
- Der Header darf keine zweite Hauptnavigation enthalten.

---

# 11. Sidebar und Navigation

## 11.1 Aufbau

Die Sidebar enthält:

- Hauptbereiche,
- Unterbereiche,
- aktive Seite,
- einklappbare Gruppen,
- optional Systemstatus im unteren Bereich.

---

## 11.2 Hauptbereiche

Vorgesehene Hauptbereiche:

```text
Main
Work
Control
Settings
```

---

## 11.3 Navigationseintrag

Jeder Eintrag enthält:

- Icon,
- Titel,
- optional Badge,
- optional Aufklappindikator.

---

## 11.4 Aktiver Zustand

Der aktive Menüpunkt wird dargestellt durch:

- leicht eingefärbten Hintergrund,
- Primärfarbe für Icon und Text,
- klaren Kontrast,
- optional eine dezente seitliche Markierung.

Nicht erlaubt:

- starke Farbverläufe,
- blinkende Markierungen,
- mehrere aktive Einträge gleichzeitig.

---

## 11.5 Eingeklappte Sidebar

Im eingeklappten Zustand:

- werden nur Icons angezeigt,
- erscheinen Bezeichnungen als Tooltip,
- bleibt der aktive Zustand erkennbar,
- bleiben alle Funktionen erreichbar.

---

# 12. Seitenkopf

Jede reguläre Inhaltsseite beginnt mit einem einheitlichen Seitenkopf.

## 12.1 Inhalt

```text
Breadcrumb
Titel
Beschreibung
Primäre Aktionen
Sekundäre Aktionen
```

---

## 12.2 Beispiel

```text
Main / Agenten

Agenten

Verwalte Agent-Server und die darauf verfügbaren Sub-Agenten.

[Agent hinzufügen] [Aktualisieren]
```

---

## 12.3 Regeln

- Pro Seite existiert genau ein H1.
- Die Beschreibung ist kurz und funktional.
- Primäre Aktionen stehen rechts.
- Es gibt maximal eine hervorgehobene Primäraktion.
- Weitere Aktionen werden sekundär oder in einem Aktionsmenü dargestellt.

---

# 13. Inhaltsbereiche

## 13.1 Standardaufbau

```text
Seitenkopf
    ↓
Optionale Status- oder Informationsleiste
    ↓
Toolbar / Filter
    ↓
Hauptinhalt
    ↓
Optionale Detailbereiche
```

---

## 13.2 Abschnittsstruktur

Lange Seiten werden in klar getrennte Abschnitte unterteilt.

Jeder Abschnitt kann enthalten:

- Titel,
- Beschreibung,
- Aktionen,
- Inhalt.

---

# 14. Karten

## 14.1 Verwendung

Karten werden verwendet für:

- zusammengehörige Informationen,
- Widgets,
- Statusübersichten,
- Einstellungen,
- kompakte Listen,
- Formularabschnitte.

---

## 14.2 Kartenaufbau

```text
+---------------------------------------------------+
| Icon  Titel                         Aktion        |
|       Beschreibung                                |
+---------------------------------------------------+
|                                                   |
| Inhalt                                            |
|                                                   |
+---------------------------------------------------+
| Optionaler Footer                                 |
+---------------------------------------------------+
```

---

## 14.3 Kartenregeln

- Radius: 12px
- Innenabstand: 20px bis 24px
- Ruhiger Hintergrund
- Dezenter Rahmen oder Schatten
- Nicht Rahmen und starken Schatten gleichzeitig verwenden
- Kartenhöhe richtet sich nach Inhalt
- Gleiche Karten innerhalb einer Reihe sollen visuell ausgerichtet sein

---

# 15. KPI- und Statistik-Karten

## 15.1 Aufbau

```text
Titel
Großer Wert
Vergleich oder Status
Optionaler Trend
```

Beispiel:

```text
Aktive Agenten
12
+2 seit gestern
```

---

## 15.2 Regeln

- Der Hauptwert ist visuell dominant.
- Vergleichswerte sind kleiner.
- Trends verwenden zusätzlich ein Icon.
- Farbliche Hervorhebung nur bei echter Bedeutung.
- Keine dekorativen Diagramme ohne Informationswert.

---

# 16. Dashboard

## 16.1 Ziel

Das Dashboard zeigt die wichtigsten Informationen des Systems auf einen Blick.

Es darf nicht zu einer ungeordneten Sammlung beliebiger Widgets werden.

---

## 16.2 Widget-Typen

```text
KPI
Status
Liste
Aktivität
Diagramm
Warnung
Systemzustand
Agentenstatus
API-Aktivität
MCP-Aktivität
```

---

## 16.3 Raster

Empfohlenes Dashboard-Raster:

```text
12 Spalten Desktop
8 Spalten Tablet
4 Spalten Mobil
```

---

## 16.4 Widget-Größen

```text
Klein: 3 Spalten
Mittel: 6 Spalten
Groß: 9 Spalten
Voll: 12 Spalten
```

---

## 16.5 Regeln

- Die wichtigsten Kennzahlen stehen oben.
- Warnungen werden vor normalen Informationen angezeigt.
- Widgets besitzen einheitliche Kopfbereiche.
- Jedes Widget benötigt einen klaren Titel.
- Widget-Aktionen sind kompakt.
- Fehlende Daten werden als Empty State dargestellt.
- Ladezustände verwenden Skeletons.

---

# 17. Buttons

## 17.1 Button-Typen

### Primär

Für die wichtigste Aktion einer Seite.

Beispiele:

```text
Speichern
Agent hinzufügen
Workflow starten
```

### Sekundär

Für ergänzende Aktionen.

Beispiele:

```text
Abbrechen
Aktualisieren
Exportieren
```

### Tertiär / Ghost

Für unauffällige Aktionen.

Beispiele:

```text
Details
Mehr anzeigen
Zurücksetzen
```

### Gefahr

Für irreversible oder riskante Aktionen.

Beispiele:

```text
Löschen
Session beenden
Agent stoppen
```

---

## 17.2 Größen

```text
Klein: 32px Höhe
Standard: 40px Höhe
Groß: 48px Höhe
```

---

## 17.3 Regeln

- Standardradius: 8px
- Iconabstand: 8px
- Primärbuttons nicht inflationär verwenden
- Löschen niemals als Primärbutton darstellen
- Icon-only Buttons benötigen Tooltip und `aria-label`
- Deaktivierte Buttons müssen klar erkennbar sein
- Ladezustand zeigt Spinner und verhindert Mehrfachklick

---

# 18. Icons

## 18.1 Iconset

Es wird genau ein Iconset verwendet.

Empfehlung:

```text
Lucide Icons
```

Alternative:

```text
Heroicons
```

---

## 18.2 Größen

```text
Klein: 14px
Standard: 18px
Navigation: 20px
Groß: 24px
Illustrativ: 32px bis 48px
```

---

## 18.3 Regeln

- Icons ergänzen Text, ersetzen ihn nicht automatisch.
- Komplexe Aktionen benötigen Text.
- Icons verwenden konsistente Strichstärken.
- Keine Emojis als funktionale Icons.
- Keine Mischung aus Outline- und Filled-Icons ohne klare Regel.

---

# 19. Badges, Tags und Statusanzeigen

## 19.1 Badge-Typen

```text
Neutral
Information
Erfolg
Warnung
Fehler
```

---

## 19.2 Beispiele

```text
Aktiv
Inaktiv
Verbunden
Getrennt
Fehler
Wartend
In Bearbeitung
Abgeschlossen
```

---

## 19.3 Regeln

- Badges sind kompakt.
- Text maximal zwei bis drei Wörter.
- Keine ausschließlich farbliche Information.
- Statusfarben sind systemweit einheitlich.
- Statusindikatoren können zusätzlich einen Punkt oder ein Icon enthalten.

---

# 20. Meldungen

## 20.1 Meldungstypen

```text
Erfolg
Information
Warnung
Fehler
```

---

## 20.2 Aufbau

```text
Icon
Titel
Beschreibung
Optionale Aktion
Schließen
```

---

## 20.3 Verwendung

### Inline-Meldung

Für Informationen innerhalb einer Seite oder eines Formulars.

### Toast

Für kurze Bestätigungen nach einer Aktion.

### Banner

Für systemweite oder besonders wichtige Hinweise.

---

## 20.4 Regeln

- Fehlermeldungen bleiben sichtbar, bis sie behoben oder geschlossen werden.
- Erfolgs-Toasts dürfen automatisch verschwinden.
- Systemkritische Meldungen werden nicht nur als Toast ausgegeben.
- Meldungen müssen verständlich formuliert sein.
- Technische Details gehören in Logs, nicht in die Benutzeroberfläche.

---

# 21. Modale Dialoge

## 21.1 Verwendung

Dialoge werden verwendet für:

- Bestätigungen,
- kurze Bearbeitungen,
- Zusatzinformationen,
- gefährliche Aktionen,
- kompakte Einstellungen.

---

## 21.2 Aufbau

```text
Titel
Beschreibung
Inhalt
Sekundäraktion
Primäraktion
```

---

## 21.3 Größen

```text
Klein: 420px
Standard: 560px
Groß: 760px
Sehr groß: 960px
```

---

## 21.4 Regeln

- Dialoge sind nicht für vollständige komplexe Seiten gedacht.
- Lange Formulare erhalten eine eigene Seite oder ein Sidepanel.
- Der Fokus wird beim Öffnen in den Dialog gesetzt.
- Escape schließt ungefährliche Dialoge.
- Kritische Aktionen benötigen eine ausdrückliche Bestätigung.
- Hintergrundinteraktion ist während des Dialogs gesperrt.

---

# 22. Sidepanels / Detailbereiche

Sidepanels werden verwendet, wenn Details betrachtet oder bearbeitet werden sollen, ohne die Hauptseite vollständig zu verlassen.

Beispiele:

- Agentendetails,
- Logeintrag,
- Benutzerinformationen,
- API-Request,
- MCP-Aufruf.

Breiten:

```text
Klein: 360px
Standard: 480px
Groß: 640px
```

---

# 23. Tabs

Tabs strukturieren zusammengehörige Inhalte auf derselben Ebene.

Beispiele:

```text
Allgemein
Berechtigungen
Sessions
Aktivitäten
```

Regeln:

- maximal sechs sichtbare Tabs,
- kurze Bezeichnungen,
- keine verschachtelten Tab-Systeme,
- aktive Auswahl eindeutig,
- Inhalt darf nicht unbemerkt verloren gehen.

---

# 24. Filterleisten und Toolbars

## 24.1 Aufbau

```text
Suche
Filter
Sortierung
Ansicht
Primäre Aktion
Sekundäre Aktionen
```

---

## 24.2 Regeln

- Häufige Filter sind direkt sichtbar.
- Seltene Filter befinden sich in einem erweiterten Filterbereich.
- Aktive Filter werden als Chips angezeigt.
- Ein Button „Filter zurücksetzen“ ist vorhanden.
- Toolbars sind responsive.
- Aktionen bleiben auch bei langen Listen erreichbar.

---

# 25. Suche

## 25.1 Globale Suche

Die globale Suche befindet sich im Header.

Sie durchsucht später zentrale Objekte wie:

- Benutzer,
- Agenten,
- Einstellungen,
- Logs,
- Module.

---

## 25.2 Lokale Suche

Eine lokale Suche bezieht sich ausschließlich auf den aktuellen Bereich.

Beispiel:

```text
Agenten durchsuchen
Benutzer durchsuchen
Logs durchsuchen
```

---

## 25.3 Regeln

- Suchfelder besitzen ein Suchicon.
- Ein klarer Lösch-Button ist vorhanden.
- Ergebnisse werden nicht nach jedem einzelnen Tastendruck aggressiv neu geladen.
- Bei serverseitiger Suche wird eine kurze Verzögerung verwendet.
- Leere Suchergebnisse erhalten einen eigenen Empty State.

---

# 26. Ladezustände

## 26.1 Skeleton Loader

Skeletons werden bevorzugt für:

- Karten,
- Tabellen,
- Dashboard-Widgets,
- Detailansichten.

---

## 26.2 Spinner

Spinner werden verwendet für:

- Buttons,
- kurze Aktionen,
- kompakte Bereiche.

---

## 26.3 Regeln

- Niemals eine leere weiße Fläche während des Ladens anzeigen.
- Layoutsprünge vermeiden.
- Bereits geladene Inhalte nicht unnötig entfernen.
- Ladeanzeigen müssen zur erwarteten Dauer passen.

---

# 27. Empty States

Ein leerer Zustand besteht aus:

- Icon oder dezenter Illustration,
- klarer Überschrift,
- kurzer Erklärung,
- optionaler Handlungsaufforderung.

Beispiel:

```text
Noch keine Agenten vorhanden

Lege den ersten Agent-Server an, um Sub-Agenten verwalten zu können.

[Agent hinzufügen]
```

Nicht erlaubt:

```text
Keine Daten
```

ohne weiteren Kontext.

---

# 28. Fehlerzustände

## 28.1 Seitenfehler

Bei einem vollständigen Seitenfehler werden angezeigt:

- verständlicher Titel,
- kurze Beschreibung,
- mögliche Lösung,
- erneuter Versuch,
- optional Rückkehr zur vorherigen Seite.

---

## 28.2 Komponentenfehler

Ein fehlerhaftes Widget darf nicht die gesamte Seite blockieren.

Es zeigt:

- Fehlerstatus,
- kurze Erklärung,
- erneute Ladeaktion.

---

## 28.3 Berechtigungsfehler

Bei fehlenden Rechten:

```text
Zugriff nicht erlaubt

Dir fehlt die notwendige Berechtigung für diesen Bereich.
```

Es werden keine internen Rechtenamen oder technischen Details angezeigt.

---

# 29. Responsive Design

## 29.1 Breakpoints

```text
Mobil: bis 639px
Tablet: 640px bis 1023px
Desktop: 1024px bis 1439px
Großer Desktop: ab 1440px
```

---

## 29.2 Mobil

- Sidebar wird zu einem Overlay-Menü.
- Seitenaktionen können in ein Aktionsmenü wechseln.
- Karten werden einspaltig.
- Tabellen werden horizontal scrollbar oder in Kartenansichten umgewandelt.
- Dialoge nutzen nahezu die volle Breite.
- Touch-Ziele besitzen mindestens 44px.

---

## 29.3 Tablet

- Sidebar kann standardmäßig eingeklappt sein.
- Dashboard verwendet weniger Spalten.
- Toolbars können umbrechen.
- Formulare können ein- oder zweispaltig sein.

---

## 29.4 Desktop

- Sidebar dauerhaft sichtbar.
- Mehrspaltige Dashboards.
- Detail-Sidepanels möglich.
- Tabellen nutzen die volle Informationsdichte.

---

# 30. Light Mode und Dark Mode

## 30.1 Grundregel

Alle Komponenten müssen in beiden Modi funktionieren.

Es darf keine Komponente geben, die nur im Light Mode oder nur im Dark Mode korrekt lesbar ist.

---

## 30.2 Umschaltung

Mögliche Modi:

```text
Hell
Dunkel
System
```

Die Auswahl wird als Benutzereinstellung gespeichert.

---

## 30.3 Regeln

- Keine fest codierten Hintergrundfarben in Komponenten.
- Farben werden über zentrale Design Tokens gesteuert.
- Kontraste müssen in beiden Modi ausreichend sein.
- Diagramme benötigen jeweils passende Farbsätze.
- Bilder und Logos müssen in beiden Modi funktionieren.

---

# 31. Animationen und Übergänge

## 31.1 Dauer

```text
Schnell: 120ms
Standard: 180ms
Lang: 240ms
```

---

## 31.2 Erlaubte Animationen

- Hover-Zustände,
- Öffnen und Schließen von Dialogen,
- Sidebar ein- und ausklappen,
- Toasts,
- Dropdowns,
- Skeleton-Shimmer,
- dezente Statusübergänge.

---

## 31.3 Nicht erlaubt

- springende Karten,
- große Zoom-Effekte,
- rotierende Hintergrundelemente,
- dauerhaft pulsierende Komponenten,
- unnötige Parallax-Effekte,
- lange Animationen über 300ms.

---

# 32. Barrierefreiheit

## 32.1 Grundregeln

- Jede Funktion ist per Tastatur erreichbar.
- Fokuszustände sind sichtbar.
- Icons ohne Text besitzen `aria-label`.
- Formulare besitzen echte Labels.
- Statusinformationen werden nicht nur über Farbe vermittelt.
- Kontraste müssen ausreichend sein.
- Modale Dialoge verwalten den Fokus korrekt.
- Navigation ist semantisch strukturiert.

---

## 32.2 Tastatur

Mindestens unterstützt:

```text
Tab
Shift + Tab
Enter
Space
Escape
Pfeiltasten in Menüs
```

---

## 32.3 Fokuszustand

Der Fokuszustand verwendet einen klar sichtbaren Ring in Primärfarbe.

Er darf nicht vollständig entfernt werden.

---

# 33. Informationsdichte

Das System besitzt drei mögliche Dichtestufen:

```text
Komfortabel
Standard
Kompakt
```

Standard ist:

```text
Standard
```

Tabellen können später optional kompakter dargestellt werden.

Formulare und Dialoge verwenden immer mindestens die Standarddichte.

---

# 34. Seitenvorlagen

## 34.1 Listenansicht

```text
Seitenkopf
Filterleiste
Tabelle oder Kartenliste
Pagination
```

---

## 34.2 Detailansicht

```text
Seitenkopf
Status
Stammdaten
Tabs
Aktivitäten
Aktionen
```

---

## 34.3 Bearbeitungsseite

```text
Seitenkopf
Formularabschnitte
Fixierte oder klar sichtbare Speicherleiste
```

---

## 34.4 Dashboardseite

```text
Seitenkopf
Statusmeldungen
KPI-Reihe
Widget-Raster
Aktivitäten
```

---

## 34.5 Einstellungsseite

```text
Seitennavigation oder Tabs
Abschnittstitel
Formularbereiche
Speichern
```

---

# 35. Agentenansicht

Die Agentenansicht unterscheidet klar zwischen:

```text
Agent-Server
Sub-Agent
```

## 35.1 Agent-Server

Darstellung mit:

- Servername,
- Status,
- Hostname,
- IP-Adresse,
- Port,
- Anzahl Sub-Agenten,
- letzte Aktivität,
- Aktionen.

## 35.2 Sub-Agent

Darstellung mit:

- Name,
- zugehöriger Agent-Server,
- Status,
- Aufgabe oder Rolle,
- letzte Aktivität,
- Aktionen.

## 35.3 Hierarchie

Sub-Agenten werden visuell dem Server zugeordnet.

Mögliche Darstellungen:

- eingerückte Liste,
- gruppierte Karten,
- Baumansicht,
- Serverkarte mit Sub-Agentenliste.

---

# 36. Logs

Logs werden technisch, aber gut lesbar dargestellt.

## 36.1 Logzeile

Enthält:

- Zeit,
- Stufe,
- Quelle,
- Benutzer oder System,
- Aktion,
- kurze Meldung,
- Details.

## 36.2 Logfarben

```text
Info: Blau
Warnung: Orange
Fehler: Rot
Kritisch: Dunkelrot
Debug: Grau
Erfolg: Grün
```

## 36.3 Detailansicht

Lange JSON-, Request- oder Fehlerdaten werden in einem monospace formatierten Bereich dargestellt.

---

# 37. Technische Daten und Code

Für technische Inhalte wird eine Monospace-Schrift verwendet.

Empfehlung:

```text
"JetBrains Mono"
"SFMono-Regular"
Consolas
monospace
```

Verwendung für:

- JSON,
- API-Requests,
- MCP-Daten,
- Logdetails,
- IDs,
- Tokens in maskierter Form,
- Codebeispiele,
- Systempfade.

---

# 38. Sicherheitsrelevante UI

## 38.1 Geheimnisse

API-Keys, MCP-Keys und Tokens werden:

- standardmäßig maskiert,
- nur einmal vollständig angezeigt,
- niemals automatisch erneut eingeblendet,
- nur nach bewusster Aktion kopiert.

---

## 38.2 Gefährliche Aktionen

Gefährliche Aktionen benötigen:

- rote Kennzeichnung,
- verständliche Erklärung,
- Bestätigung,
- bei besonders kritischen Aktionen zusätzliche Texteingabe.

Beispiel:

```text
Zum endgültigen Löschen „LÖSCHEN“ eingeben.
```

---

# 39. Do's

- Bestehende Komponenten wiederverwenden.
- Klare Seitenhierarchie schaffen.
- Pro Seite eine Primäraktion definieren.
- Status verständlich darstellen.
- Lade-, Leer- und Fehlerzustände berücksichtigen.
- Design Tokens verwenden.
- Responsive Verhalten mitdenken.
- Rechte und deaktivierte Zustände sichtbar machen.
- Technische Inhalte lesbar formatieren.

---

# 40. Don'ts

- Keine individuellen Farben pro Modul.
- Keine neuen Schriftarten.
- Keine inkonsistenten Buttonformen.
- Keine versteckten Hauptaktionen.
- Keine Browser-Standarddialoge.
- Keine unbeschrifteten Icons für komplexe Aktionen.
- Keine Tabelle ohne Empty State.
- Kein Formular ohne klare Fehlermeldungen.
- Keine dauerhaft blinkenden Statusanzeigen.
- Keine doppelten UI-Komponenten.
- Keine unnötigen Animationen.
- Keine ungeprüften externen UI-Frameworks.

---

# 41. Dateistruktur der UI

Empfohlene Grundstruktur:

```text
/css/
    style.css
    layout.css
    navigation.css
    komponenten.css
    dashboard.css
    formulare.css
    tabellen.css
    responsive.css

/js/
    app.js
    navigation.js
    dialoge.js
    meldungen.js
    dashboard.js
    formulare.js
    tabellen.js
```

Die exakte CSS-Struktur wird in `14_CSS_Standards.md` definiert.

---

# 42. Regeln für neue Module

Vor der Entwicklung eines neuen Moduls muss der Agent prüfen:

1. Welche Seitenvorlage passt?
2. Welche vorhandenen Komponenten können verwendet werden?
3. Welche Zustände müssen dargestellt werden?
4. Welche Rechte beeinflussen die Oberfläche?
5. Welche Aktionen sind primär, sekundär oder gefährlich?
6. Wie verhält sich die Seite auf Mobilgeräten?
7. Welche Lade-, Leer- und Fehlerzustände werden benötigt?
8. Welche Daten gehören in Tabellen, Karten oder Detailpanels?

Neue Module dürfen keine eigene Designsprache einführen.

---

# 43. Qualitätsprüfung einer neuen Seite

Eine Seite gilt erst als fertig, wenn folgende Punkte geprüft wurden:

```text
[ ] Einheitlicher Seitenkopf
[ ] Genau eine Primäraktion
[ ] Passende Icons
[ ] Standardabstände eingehalten
[ ] Light Mode geprüft
[ ] Dark Mode geprüft
[ ] Desktop geprüft
[ ] Tablet geprüft
[ ] Mobil geprüft
[ ] Tastaturbedienung geprüft
[ ] Ladezustand vorhanden
[ ] Empty State vorhanden
[ ] Fehlerzustand vorhanden
[ ] Rechtezustände berücksichtigt
[ ] Keine doppelte Komponente erstellt
[ ] Keine fest codierten Farben verwendet
[ ] Keine technischen Fehlermeldungen sichtbar
```

---

# 44. Zusammenfassung

Das NoobClaw-Webinterface verwendet ein einheitliches, modernes und professionelles Designsystem.

Die Oberfläche basiert auf:

- klarer Informationshierarchie,
- ruhiger Farbgebung,
- konsistenten Komponenten,
- festen Design Tokens,
- einheitlichen Seitenvorlagen,
- vollständiger Responsive-Unterstützung,
- Light und Dark Mode,
- zugänglicher Bedienung,
- klaren Zuständen,
- wiederverwendbaren UI-Bausteinen.

Das Design darf nicht für einzelne Module neu erfunden werden.

Jede neue Seite muss so wirken, als wäre sie von Anfang an Teil derselben Anwendung gewesen.