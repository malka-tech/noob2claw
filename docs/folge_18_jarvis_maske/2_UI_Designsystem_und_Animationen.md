# Folge 18 – UI-Designsystem und Animationen

## 1. Gestaltungsziel

Die Jarvis-Maske soll sich wie die Sprachzentrale eines hochwertigen Science-Fiction-Spiels anfühlen, aber als echte Webanwendung verständlich, schnell und zugänglich bleiben.

```text
cinematisch
+ reaktiv
+ hochwertig
+ klar bedienbar
+ technisch realisierbar
```

Das Design ist keine Kopie einer Film-, Spiele- oder Social-Media-Oberfläche. Es verwendet die Noob2Claw-Funktionalität und eine eigene visuelle Sprache.

---

## 2. Visuelle Referenzen

### Benutzerentwurf


Stärken des Entwurfs:

- klare Dreiteilung,
- Agent im Mittelpunkt,
- Transkript bleibt sichtbar,
- großer Sprachbutton,
- deutliche Gesprächsaktion.

Weiterentwicklung:

- Agentenübersicht direkt integrieren,
- visuelle Hierarchie stärker auf den aktuellen Gesprächszustand ausrichten,
- Animationen nicht nur dekorativ, sondern zustandsgebunden verwenden,
- weniger gleichförmige Karten,
- mehr Tiefe durch Ebenen, Licht und Bewegung,
- Zustände auch durch Text und Symbol verständlich machen,
- Noob2Claw als eigene Marke statt einer Kopie etablieren.

### Neues Konzept


Das Bild ist ein Richtungsentwurf. Die technische Umsetzung darf Details an vorhandene Navigation, Typografie und Agentenbilder anpassen. Informationshierarchie, Zustandslogik und Bedienbarkeit sind wichtiger als pixelgenaues Nachbauen.

---

## 3. Die zwei Hauptansichten

### 3.1 Agenten-Lobby

Beim Öffnen des Navigationspunkts erscheint zunächst eine Übersicht aller erlaubten aktiven Agenten.

Inhalt:

- großer Titel `Wähle deinen Agenten`,
- Suchfeld ab einer sinnvollen Anzahl von Agenten,
- Agentenkarten mit Bild, Name und kurzer Beschreibung,
- Zustand der benötigten Sprachkonfiguration,
- zuletzt verwendeter Agent hervorgehoben,
- Aktion `Gespräch starten`,
- verständlicher Hinweis, wenn STT, TTS oder Stimme fehlen.

Die Karten dürfen schwebend und holografisch wirken, müssen aber eine klare Leserichtung behalten. Der ausgewählte Agent erhält eine starke Fokusfläche; nicht ausgewählte Karten treten visuell zurück.

### 3.2 Voice Link

Nach Auswahl eines Agenten öffnet sich die Gesprächsansicht:

```text
┌──────────────┬──────────────────────────────┬──────────────────┐
│ Agenten-Dock │       Agenten-Kern           │    Transkript     │
│              │                              │                   │
│ Auswahl      │  Avatar + Energie + Status   │ Nachrichten       │
│ Session      │  Waveform + Live-Entwurf     │ Eingabe/Fehler    │
│ Technik      │  Push-to-Talk-Controller     │ Senden            │
└──────────────┴──────────────────────────────┴──────────────────┘
```

Der Agentenkern ist der visuelle Mittelpunkt. Agenten-Dock und Transkript unterstützen ihn, konkurrieren aber nicht mit ihm.

---

## 4. Layoutsystem

### Große Desktopansicht ab ungefähr 1280 Pixeln

- linkes Agenten-Dock: 16 bis 19 Prozent,
- zentrale Bühne: 50 bis 57 Prozent,
- Transkript: 27 bis 32 Prozent,
- obere Systemleiste über volle Breite,
- Push-to-Talk im unteren Zentrum,
- gleichmäßige Außenabstände,
- Höhe vollständig nutzen, ohne wichtige Aktionen unter den Fold zu schieben.

### Mittlere Ansicht

- Agenten-Dock auf kompakte Avatare reduzieren,
- Transkript als ein- und ausblendbares Seitenpanel,
- Agentenkern behält Priorität,
- zentrale Aktionen bleiben dauerhaft erreichbar.

### Kleine Ansicht

- Agentenwahl als horizontaler Streifen oder eigenes Overlay,
- Transkript als Bottom Sheet,
- Mikrofonbutton in Daumenreichweite,
- keine erzwungene Desktopverkleinerung,
- Partikel und sekundäre HUD-Details reduzieren.

Die Desktopansicht ist die visuelle Leitansicht der Folge. Responsive Zustände dürfen funktional weniger aufwendig wirken, müssen aber vollständig bedienbar bleiben.

---

## 5. Farbsystem

Das zentrale Variablen- und Themesystem aus Folge 9 bleibt die Quelle für Farben,
Abstände, Typografie, Rahmen und Zustände. Die Jarvis-Maske erweitert es nur dort,
wo ein spezieller Effekt keinen passenden zentralen Token besitzt. Neue Variablen
werden aus zentralen Tokens abgeleitet und nicht als zweites Themesystem gepflegt.

Beispielhafte Zuordnung für ausschließlich Jarvis-spezifische Effekte:

```css
:root {
    --farbe-jarvis-hintergrund-dunkel: var(--farbe-hintergrund-dunkel, #02060c);
    --farbe-jarvis-flaeche: var(--farbe-flaeche, rgba(8, 24, 43, 0.72));
    --farbe-jarvis-linie: var(--farbe-rahmen, rgba(113, 191, 255, 0.24));
    --farbe-jarvis-text: var(--farbe-text, #f3f8ff);
    --farbe-jarvis-text-dezent: var(--farbe-text-dezent, #9fb2c8);
    --farbe-jarvis-akzent: var(--farbe-akzent, #35d7ff);
    --farbe-jarvis-erfolg: var(--farbe-erfolg, #35f2a4);
    --farbe-jarvis-warnung: var(--farbe-warnung, #ffb84d);
    --farbe-jarvis-fehler: var(--farbe-fehler, #ff4d66);
}
```

Die endgültigen Namen der referenzierten zentralen Tokens werden aus dem aktiven
Projekt übernommen. Bereits vorhandene Variablen werden nicht dupliziert.

Farbbedeutung:

| Farbe | Bedeutung |
|---|---|
| Cyan | bereit, verbunden, neutrale Energie |
| Cyan + Violett | Benutzer spricht beziehungsweise Aufnahme läuft |
| Amber | Verarbeitung, Warten, kontrollierte Warnung |
| Violett + Magenta | Agent denkt oder spricht |
| Grün | erfolgreiche Verbindung beziehungsweise erfolgreicher Abschluss |
| Rot | Gespräch beenden oder schwerer Fehler |

Farbe ist nie der einzige Zustandsindikator. Statuswort, Symbol und Animation ergänzen sie.

---

## 6. Typografie

- vorhandene Projektschrift bevorzugen,
- breite, klare Groteskschrift für Titel und Status,
- Monospace nur sparsam für echte technische Werte,
- Status `ICH HÖRE ZU` groß und sofort lesbar,
- kein künstlicher Zeichensalat als Dekoration,
- ausreichende Kontraste auf allen Glasflächen,
- Textschatten nur zur Trennung vom Hintergrund, nicht als Effektfeuerwerk.

Empfohlene Hierarchie:

```text
Hauptstatus       34–52 px
Ansichtstitel     24–32 px
Agentenname       18–24 px
Nachrichtentext   16–19 px
Metadaten         12–14 px
```

Die Werte werden responsiv über `clamp()` skaliert.

---

## 7. Ebenen und Materialien

### Ebene 1 – Raum

- fast schwarzer Verlauf,
- sehr langsame Partikel,
- dezentes perspektivisches Raster,
- Vignette zur Fokussierung.

### Ebene 2 – System

- feine Linien,
- wenige Messpunkte,
- echte Statuswerte,
- keine erfundenen Telemetriedaten.

### Ebene 3 – Glasflächen

- dunkle transparente Flächen,
- weiche innere Kante,
- dezente Lichtbrechung,
- klare Textkontraste,
- Fallback ohne `backdrop-filter`.

### Ebene 4 – Energie

- Agentenhalo,
- Orbitlinien,
- Waveform,
- Statuspulse,
- nur in relevanten Zuständen stark sichtbar.

### Ebene 5 – Interaktion

- Mikrofon-Controller,
- Senden,
- Stoppen,
- Agentenauswahl,
- Fokusrahmen und Fehlermeldungen.

Interaktive Elemente bleiben immer über dekorativen Ebenen.

---

## 8. Hauptkomponenten

### `jarvis-rahmen`

- vollständige Anwendungsebene,
- enthält Hintergrund, Systemleiste und aktuelle Ansicht,
- setzt Theme-Variablen und Performance-Modus.

### `agenten-lobby`

- rendert erlaubte Agenten,
- Suche und Tastaturnavigation,
- startet beziehungsweise öffnet den ausgewählten Chat.

### `agenten-leiste`

- kompakte Agentenliste während des Gesprächs,
- aktive Auswahl eindeutig,
- Wechsel nur nach Behandlung laufender Aufnahme oder ungesendetem Entwurf.

### `agenten-kern`

- Agentenbild,
- Halo und Statusringe,
- Waveform beziehungsweise Sprachenergie,
- zentraler Status und kurzer Untertitel.

### `sprachsteuerung`

- großer Mikrofonbutton,
- Leertastenhinweis,
- Timer,
- Audiopegel,
- Stoppen, Verwerfen, erneutes Aufnehmen und Senden.

### `transkript-bereich`

- bestehende Chatnachrichten,
- Scrollposition und ungelesene neue Nachrichten,
- Transkriptionsentwurf,
- Fehler und Wiederholen,
- sichere Textdarstellung.

### `sitzungsanzeige`

- Agent, Dauer und echter Verbindungszustand,
- Lautsprecherstatus,
- Bewegungs- beziehungsweise Leistungsmodus,
- `Gespräch beenden`.

---

## 9. Fachliche UI-Zustände

```text
LOBBY
READY
ARMING
LISTENING
TRANSCRIBING
REVIEW
SENDING
AGENT_THINKING
VOICE_GENERATING
AGENT_SPEAKING
ERROR
ENDING
ENDED
```

Jeder Zustand besitzt genau:

- ein primäres Statuswort,
- eine Statusfarbe,
- eine Hauptanimation,
- erlaubte Aktionen,
- eine sichere Abbruchaktion,
- einen Screenreader-Text.

---

## 10. Zustandsdarstellung

| Zustand | Haupttext | Farbe | Kernbewegung | Hauptaktion |
|---|---|---|---|---|
| `READY` | Bereit | Cyan | langsames Atmen | sprechen |
| `ARMING` | Mikrofon wird aktiviert | Cyan | Ring baut sich auf | abbrechen |
| `LISTENING` | Ich höre zu | Cyan/Violett | Audio reagiert nach außen | loslassen/stoppen |
| `TRANSCRIBING` | Ich verstehe dich | Amber | innerer Ring scannt | abbrechen, falls möglich |
| `REVIEW` | Prüfe deine Nachricht | Cyan | Kern ruhig | senden oder neu aufnehmen |
| `SENDING` | Nachricht wird gesendet | Amber | kurzer Impuls | warten |
| `AGENT_THINKING` | Agent denkt nach | Violett | Orbits drehen versetzt | stoppen/zurück |
| `VOICE_GENERATING` | Stimme wird vorbereitet | Violett | Spektrallinie lädt | warten |
| `AGENT_SPEAKING` | Agent spricht | Magenta/Cyan | Waveform strahlt nach außen | stumm/stoppen |
| `ERROR` | Aktion nicht möglich | Amber/Rot | Animation beruhigt | wiederholen/zurück |
| `ENDING` | Gespräch wird beendet | Neutral | Energie fährt herunter | warten |

Die Oberfläche zeigt keine Formulierung wie „Live-Transkription“, wenn die Integration nur eine abgeschlossene Aufnahme transkribiert.

---

## 11. Animationen

### Hintergrundpartikel

- langsam und unaufdringlich,
- geringe Deckkraft,
- keine zufälligen hellen Blitze,
- Partikelzahl nach Leistungsmodus skalieren.

### Orbitlinien

- zwei bis vier Ebenen mit unterschiedlichen Geschwindigkeiten,
- keine permanent hektische Bewegung,
- beim Zustandswechsel sanft beschleunigen oder abbremsen,
- im reduzierten Modus statisch.

### Agentenhalo

- sehr langsamer Grundpuls in `READY`,
- pegelabhängige Ausdehnung in `LISTENING`,
- spektrale Ausdehnung in `AGENT_SPEAKING`,
- amberfarbener Sweep in `TRANSCRIBING`,
- keine harten Helligkeitssprünge.

### Statuswechsel

- Lichtfarbe über 250 bis 450 Millisekunden überblenden,
- Statuswort mit kurzer Opacity- und Translate-Bewegung,
- keine komplette Seite bei jedem Wechsel neu animieren.

### Transkriptnachrichten

- 180 bis 260 Millisekunden sanft einblenden,
- kein springendes Layout,
- automatische Scrollbewegung nur, wenn der Benutzer bereits am unteren Rand ist,
- sonst Hinweis `Neue Nachricht`.

---

## 12. Reaktive Audioeffekte

Der lokale Mikrofonstream kann über einen `AnalyserNode` Pegel- und Zeitbereichsdaten für die Visualisierung liefern.

Verwendung:

- Mikrofonpegel,
- zentrale Waveform,
- Halo-Skalierung in engen Grenzen,
- Partikelimpuls bei Sprachenergie.

Nicht verwenden für:

- versteckte Spracherkennung im Browser,
- dauerhafte Speicherung von Analysewerten,
- Übertragung zusätzlicher Rohdaten,
- irreführende perfekte Lippenbewegung.

Beim Agenten-Audio kann dieselbe Visualisierung aus der abgespielten Audiospur gespeist werden, sofern CORS und vorhandene Dateiauslieferung dies erlauben. Andernfalls wird eine deterministische, nicht irreführende Abspielanimation verwendet.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode
[/Sources]

---

## 13. Push-to-Talk-Visualisierung

### Leertaste

- `keydown`: äußerer Ring zieht sich kurz zusammen und Aufnahme beginnt,
- gehalten: Puls und Waveform reagieren,
- `keyup`: Ring löst sich, Aufnahme endet und Verarbeitung beginnt,
- `Escape`, Fensterverlust oder versteckte Seite: sichere Beendigung.

### Mikrofonbutton

- bereit: ruhiger Cyan-Rand,
- Hover/Fokus: heller, aber ohne Größenflackern,
- aktiv: Cyan-Violett-Energiekern,
- Verarbeitung: Amber-Segment läuft einmal kontrolliert um den Rand,
- Fehler: kurze ruhige Warnmarkierung statt hektischem Schütteln.

Der Button behält immer eine sichtbare Beschriftung oder einen zugänglichen Namen. Die Leertaste wird nicht abgefangen, wenn der Fokus in einem Eingabefeld, Textbereich, Link oder interaktiven Steuerelement liegt.

---

## 14. Bewegungsreduktion

Die Systemeinstellung `prefers-reduced-motion: reduce` wird automatisch respektiert. Zusätzlich darf die Maske einen eigenen Schalter `Effekte reduzieren` anbieten.

Im reduzierten Modus:

- Partikel und Parallax deaktivieren,
- Orbits anhalten,
- Skalierungs- und Kamerabewegungen entfernen,
- Zustände nur durch Farbe, Text, Symbol und sanfte Opacity zeigen,
- Waveform als einfache Pegelanzeige darstellen,
- keine Funktion entfernen.

```css
@media (prefers-reduced-motion: reduce) {
    .jarvis-partikelfeld,
    .jarvis-umlaufbahn {
        animation: none;
    }
}
```

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@media/prefers-reduced-motion
[/Sources]

---

## 15. Keine gefährlichen Lichtblitze

- keine Stroboskop-Effekte,
- keine schnellen großflächigen Weißwechsel,
- keine aggressiven roten Blitzfolgen,
- Alarmzustand durch statische rote Kante, Symbol und Text,
- animierte Helligkeitswechsel deutlich unter kritischen Frequenzen halten,
- das Design idealerweise vollständig ohne echte Blitzanimationen umsetzen.

Die W3C-Technik G19 beschreibt als einfache strenge Prüfmethode höchstens drei Lichtblitze innerhalb einer Sekunde. Für diese Maske ist die bessere Designentscheidung, dekorative Blitze vollständig zu vermeiden.

[Sources]
- https://www.w3.org/WAI/WCAG21/Techniques/general/G19
[/Sources]

---

## 16. Performance-Modi

### Hoch

- volle Partikelzahl,
- Canvas-Waveform,
- mehrere Orbits,
- Blur und Glow in kontrolliertem Umfang.

### Standard

- reduzierte Partikelzahl,
- maximal zwei aktive Orbits,
- begrenzte Blur-Radien,
- normale Waveform.

### Sparsam

- keine Partikel,
- statischer Hintergrund,
- einfache Balkenanzeige,
- minimale Schatten,
- Statuswechsel nur über Opacity.

Der Standardmodus ist die Voreinstellung. Ein automatischer Wechsel darf nur auf klaren, stabilen Messwerten basieren und muss manuell überschreibbar sein.

---

## 17. Technische Animationsregeln

- CSS-Animationen bevorzugt auf `transform` und `opacity` beschränken.
- Audio-Waveform in einem einzelnen Canvas statt in hunderten DOM-Elementen zeichnen.
- `requestAnimationFrame()` für framegebundene Canvas-Updates verwenden.
- Fortschritt zeitbasiert berechnen, nicht pro Frame konstant erhöhen.
- Canvas-Auflösung an Gerätepixelverhältnis anpassen, aber nach oben begrenzen.
- Animationen bei `document.hidden` pausieren.
- Audio und Mikrofon bei Seitenwechsel sicher beenden.
- Event Listener und AudioContext beim Verlassen der Maske entfernen beziehungsweise schließen.
- keine dauerhaft laufenden Timer nach Gesprächsende.

Die Page Visibility API meldet, wenn eine Seite verborgen wird. Browser drosseln Hintergrund-Timer und pausieren üblicherweise `requestAnimationFrame()`, weshalb der Zustand nicht nur aus Animationsframes abgeleitet werden darf.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API
- https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame
[/Sources]

---

## 18. Fokus und Interaktion

- sichtbare Fokusrahmen in Cyan oder Weiß,
- Fokusreihenfolge folgt Agenten-Dock → zentrale Aktionen → Transkript,
- Modaldialoge fangen Fokus kontrolliert ein und geben ihn zurück,
- Agentenwechsel mit ungesendetem Entwurf benötigt Bestätigung,
- `Gespräch beenden` ist klar, aber nicht versehentlich neben `Senden`,
- Escape bricht zuerst die aktuelle flüchtige Aktion ab und beendet nicht sofort das gesamte Gespräch,
- alle Icons besitzen zugängliche Namen und Tooltips,
- Drag-Gesten sind nie der einzige Bedienweg.

---

## 19. Leere und fehlerhafte Zustände

### Keine Agenten

```text
Keine verfügbaren Agenten
Lege zuerst einen aktiven Agenten an oder prüfe deine Berechtigung.
```

### STT fehlt

```text
Spracheingabe ist noch nicht eingerichtet.
Wähle eine globale Speech-to-Text-Integration.
```

### TTS oder Stimme fehlt

```text
Dieser Agent kann antworten, aber noch nicht sprechen.
Prüfe Text-to-Speech und die Agentenstimme.
```

### Mikrofon blockiert

```text
Mikrofonzugriff wurde nicht erlaubt.
Du kannst die Berechtigung ändern oder per Tastatur schreiben.
```

### Verbindung unterbrochen

```text
Verbindung unterbrochen
Der Entwurf bleibt erhalten. Erneut verbinden oder Gespräch beenden.
```

Fehlerzustände reduzieren dekorative Animationen und bringen Handlung und Erklärung in den Vordergrund.

---

## 20. Visuelle Abnahme

- [ ] Agentenübersicht zeigt alle erlaubten aktiven Agenten.
- [ ] ausgewählter Agent ist ohne reine Farbcodierung erkennbar.
- [ ] aktueller Gesprächszustand ist innerhalb einer Sekunde verständlich.
- [ ] Mikrofonbutton und Leertastenhinweis sind sofort auffindbar.
- [ ] Transkript bleibt auch bei langen Nachrichten lesbar.
- [ ] wichtigste Aktionen funktionieren bei 200 Prozent Zoom.
- [ ] kleine Viewports besitzen eine echte responsive Anordnung.
- [ ] reduzierte Bewegung entfernt nicht notwendige Animationen.
- [ ] keine schnellen Lichtblitze oder Stroboskop-Effekte.
- [ ] Standardmodus bleibt auf durchschnittlicher Hardware flüssig.
- [ ] Hintergrund-Tab verbraucht keine unnötige Animationsleistung.
- [ ] alle angezeigten Status- und Latenzwerte stammen aus echten Daten.
- [ ] keine fremden Marken oder geschützten Figuren werden nachgebaut.
