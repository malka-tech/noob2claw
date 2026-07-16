# 14_CSS_Standards.md

# CSS-Standards

**Version:** 1.0  
**Projekt:** NoobClaw Webinterface  
**Gültigkeit:** Verbindlich für alle CSS-Dateien, Komponenten, Seiten und Module

---

# 1. Zweck dieses Dokuments

Dieses Dokument definiert die verbindlichen technischen Regeln für das CSS der Anwendung.

Es beschreibt:

- die CSS-Dateistruktur,
- die Verwendung von Design Tokens,
- die Benennung von Klassen,
- die Kaskade und Spezifität,
- Light Mode und Dark Mode,
- Layout- und Grid-Regeln,
- Komponentenstile,
- Responsive Design,
- Barrierefreiheit,
- Animationen,
- Performance,
- Qualitätsprüfungen.

Die optischen Grundlagen werden in folgender Datei beschrieben:

```text
13_Design_und_UI_Standards.md
```

Formulare werden zusätzlich beschrieben in:

```text
15_Formular_Standards.md
```

Tabellen werden zusätzlich beschrieben in:

```text
16_Tabellen_Standards.md
```

---

# 2. Ziel

Das CSS soll:

- konsistent,
- wartbar,
- modular,
- wiederverwendbar,
- performant,
- verständlich,
- erweiterbar,
- responsiv,
- barrierearm

sein.

Neue Module dürfen kein eigenes isoliertes Styling-System einführen.

---

# 3. Grundprinzipien

## 3.1 Design Tokens statt Einzelwerte

Farben, Abstände, Radien, Schatten, Schriftgrößen und Übergänge werden nicht mehrfach hart codiert.

Sie werden über zentrale CSS-Variablen definiert.

Nicht erlaubt:

```css
.meine-karte {
    border-radius: 13px;
    background: #ffffff;
    color: #111827;
}
```

Vorgeschrieben:

```css
.karte {
    border-radius: var(--radius-karte);
    background: var(--farbe-flaeche);
    color: var(--farbe-text);
}
```

---

## 3.2 Klassen statt Elementselektoren

Komponenten werden über Klassen gestaltet.

Nicht erlaubt:

```css
main section div button {
    background: blue;
}
```

Vorgeschrieben:

```css
.button--primaer {
    background: var(--farbe-primaer);
}
```

---

## 3.3 Niedrige Spezifität

Selektoren bleiben bewusst kurz und flach.

Ziel:

```text
0 oder 1 Klassenebene
```

Bevorzugt:

```css
.karte {}
.karte__kopf {}
.karte__inhalt {}
```

Nicht:

```css
.app .inhalt .bereich .karte .karte-kopf h3 {}
```

---

## 3.4 Kein Styling über IDs

IDs sind für JavaScript, Anker oder eindeutige technische Zuordnungen vorgesehen.

Nicht erlaubt:

```css
#hauptnavigation {}
```

Vorgeschrieben:

```css
.hauptnavigation {}
```

---

## 3.5 Kein Inline-CSS

Nicht erlaubt:

```html
<div style="margin-top: 17px; color: red;">
```

Alle Styles gehören in CSS-Dateien oder klar definierte CSS Custom Properties.

Ausnahme:

Dynamisch berechnete Werte dürfen über CSS-Variablen gesetzt werden.

Beispiel:

```html
<div class="fortschritt" style="--fortschritt: 65%;">
```

---

# 4. CSS-Dateistruktur

Empfohlene Struktur:

```text
/css/
├── tokens.css
├── reset.css
├── basis.css
├── layout.css
├── navigation.css
├── komponenten.css
├── dashboard.css
├── formulare.css
├── tabellen.css
├── hilfsklassen.css
├── responsive.css
└── druck.css
```

---

# 5. Aufgaben der CSS-Dateien

## 5.1 `tokens.css`

Enthält ausschließlich Design Tokens:

- Farben,
- Abstände,
- Radien,
- Schatten,
- Schriftgrößen,
- Zeilenhöhen,
- Z-Index-Werte,
- Breakpoints als Dokumentation,
- Animationszeiten.

---

## 5.2 `reset.css`

Enthält den technischen CSS-Reset.

Aufgaben:

- Box-Sizing,
- Standardabstände entfernen,
- Formulare angleichen,
- Medien responsiv machen,
- Typografie normalisieren.

---

## 5.3 `basis.css`

Enthält globale Basiselemente:

- `html`,
- `body`,
- Überschriften,
- Absätze,
- Links,
- Code,
- Listen,
- Auswahlmarkierung,
- Fokuszustände.

---

## 5.4 `layout.css`

Enthält ausschließlich Layoutstrukturen:

- App-Shell,
- Header,
- Sidebar,
- Inhaltsbereich,
- Seitenkopf,
- Container,
- Raster,
- Panels.

---

## 5.5 `navigation.css`

Enthält:

- Hauptnavigation,
- Untermenüs,
- aktive Navigation,
- eingeklappte Sidebar,
- mobile Navigation,
- Breadcrumbs.

---

## 5.6 `komponenten.css`

Enthält allgemeine UI-Komponenten:

- Buttons,
- Karten,
- Badges,
- Meldungen,
- Dialoge,
- Tabs,
- Tooltips,
- Dropdowns,
- Sidepanels,
- Pagination,
- Statusanzeigen.

---

## 5.7 `dashboard.css`

Enthält ausschließlich dashboardbezogene Styles:

- Widget-Raster,
- KPI-Karten,
- Statuskacheln,
- Aktivitätslisten,
- Dashboard-Anordnung.

---

## 5.8 `formulare.css`

Enthält alle Formularstile.

Die fachlichen Regeln stehen in:

```text
15_Formular_Standards.md
```

---

## 5.9 `tabellen.css`

Enthält alle Tabellen- und Listenstile.

Die fachlichen Regeln stehen in:

```text
16_Tabellen_Standards.md
```

---

## 5.10 `hilfsklassen.css`

Enthält eine kleine, kontrollierte Menge von Utility-Klassen.

Beispiele:

```text
.sr-only
.text-rechts
.text-mitte
.versteckt
.flex
.flex-spalte
.abstand-8
.abstand-16
```

Es wird kein vollständiges Utility-Framework nachgebaut.

---

## 5.11 `responsive.css`

Enthält nur Regeln, die mehrere Bereiche betreffen.

Komponentenspezifische Responsive-Regeln dürfen direkt bei der jeweiligen Komponente stehen.

---

## 5.12 `druck.css`

Enthält Druckregeln.

Nicht druckbare Elemente:

- Navigation,
- Buttons,
- Dialoge,
- Toasts,
- Toolbars.

---

# 6. Einbindereihenfolge

Die Dateien werden in folgender Reihenfolge geladen:

```text
1. tokens.css
2. reset.css
3. basis.css
4. layout.css
5. navigation.css
6. komponenten.css
7. dashboard.css
8. formulare.css
9. tabellen.css
10. hilfsklassen.css
11. responsive.css
12. druck.css
```

Die Reihenfolge darf nicht pro Seite verändert werden.

---

# 7. Cascade Layers

Wenn vom Zielbrowser unterstützt, werden CSS Cascade Layers verwendet.

Empfohlene Reihenfolge:

```css
@layer reset, tokens, basis, layout, komponenten, module, utilities, overrides;
```

Beispiel:

```css
@layer komponenten {
    .karte {
        background: var(--farbe-flaeche);
    }
}
```

`overrides` darf nur für bewusst dokumentierte Sonderfälle verwendet werden.

---

# 8. Design Tokens

## 8.1 Root-Definition

Alle globalen Tokens werden in `:root` definiert.

Beispiel:

```css
:root {
    --farbe-primaer: #2563eb;
    --farbe-primaer-hover: #1d4ed8;
    --farbe-primaer-aktiv: #1e40af;

    --farbe-erfolg: #16a34a;
    --farbe-warnung: #f59e0b;
    --farbe-fehler: #dc2626;
    --farbe-info: #0ea5e9;

    --farbe-seite: #f8fafc;
    --farbe-flaeche: #ffffff;
    --farbe-flaeche-sekundaer: #f1f5f9;
    --farbe-rahmen: #e2e8f0;

    --farbe-text: #0f172a;
    --farbe-text-sekundaer: #475569;
    --farbe-text-deaktiviert: #94a3b8;
}
```

---

## 8.2 Abstände

```css
:root {
    --abstand-1: 4px;
    --abstand-2: 8px;
    --abstand-3: 12px;
    --abstand-4: 16px;
    --abstand-5: 20px;
    --abstand-6: 24px;
    --abstand-8: 32px;
    --abstand-10: 40px;
    --abstand-12: 48px;
    --abstand-16: 64px;
}
```

---

## 8.3 Radien

```css
:root {
    --radius-klein: 6px;
    --radius-standard: 8px;
    --radius-karte: 12px;
    --radius-dialog: 14px;
    --radius-panel: 16px;
    --radius-rund: 9999px;
}
```

---

## 8.4 Schatten

```css
:root {
    --schatten-klein: 0 1px 2px rgb(15 23 42 / 0.06);
    --schatten-standard: 0 4px 12px rgb(15 23 42 / 0.08);
    --schatten-gross: 0 16px 40px rgb(15 23 42 / 0.14);
    --schatten-dialog: 0 24px 80px rgb(15 23 42 / 0.24);
}
```

Schatten werden zurückhaltend verwendet.

---

## 8.5 Typografie

```css
:root {
    --schrift-standard:
        Inter,
        system-ui,
        -apple-system,
        BlinkMacSystemFont,
        "Segoe UI",
        Arial,
        sans-serif;

    --schrift-monospace:
        "JetBrains Mono",
        "SFMono-Regular",
        Consolas,
        monospace;

    --schriftgroesse-1: 12px;
    --schriftgroesse-2: 13px;
    --schriftgroesse-3: 14px;
    --schriftgroesse-4: 16px;
    --schriftgroesse-5: 20px;
    --schriftgroesse-6: 24px;
    --schriftgroesse-7: 30px;
}
```

---

## 8.6 Übergänge

```css
:root {
    --dauer-schnell: 120ms;
    --dauer-standard: 180ms;
    --dauer-lang: 240ms;

    --easing-standard: cubic-bezier(0.2, 0, 0, 1);
}
```

---

## 8.7 Layoutwerte

```css
:root {
    --hoehe-header: 64px;
    --breite-sidebar: 256px;
    --breite-sidebar-kompakt: 72px;
    --breite-inhalt-max: 1600px;
    --breite-formular-max: 960px;
}
```

---

# 9. Light Mode und Dark Mode

## 9.1 Standardmodus

Der Standardmodus wird in `:root` definiert.

---

## 9.2 Dark Mode

Dark Mode wird über ein Datenattribut gesteuert:

```html
<html data-theme="dunkel">
```

CSS:

```css
[data-theme="dunkel"] {
    --farbe-seite: #0b1120;
    --farbe-flaeche: #111827;
    --farbe-flaeche-sekundaer: #1e293b;
    --farbe-rahmen: #273449;

    --farbe-text: #f8fafc;
    --farbe-text-sekundaer: #cbd5e1;
    --farbe-text-deaktiviert: #64748b;
}
```

---

## 9.3 Systemmodus

Systemmodus kann zusätzlich über `prefers-color-scheme` berücksichtigt werden.

```css
@media (prefers-color-scheme: dark) {
    html:not([data-theme]) {
        /* Dark-Mode-Tokens */
    }
}
```

---

## 9.4 Regeln

Nicht erlaubt:

```css
.karte {
    background: white;
}
```

Vorgeschrieben:

```css
.karte {
    background: var(--farbe-flaeche);
}
```

---

# 10. Reset

Empfohlener Grundreset:

```css
*,
*::before,
*::after {
    box-sizing: border-box;
}

html {
    min-height: 100%;
    text-size-adjust: 100%;
}

body {
    min-height: 100%;
    margin: 0;
}

img,
svg,
video,
canvas {
    display: block;
    max-width: 100%;
}

button,
input,
select,
textarea {
    font: inherit;
}

button {
    cursor: pointer;
}
```

---

# 11. Globale Basis

```css
body {
    background: var(--farbe-seite);
    color: var(--farbe-text);
    font-family: var(--schrift-standard);
    font-size: var(--schriftgroesse-3);
    line-height: 1.5;
}
```

---

# 12. Klassennamen

## 12.1 Sprache

Projektbezogene CSS-Klassen werden auf Deutsch benannt.

Technische Standardbegriffe dürfen verwendet werden, wenn sie eindeutig und etabliert sind.

Bevorzugt:

```text
.seitenkopf
.karte
.button
.hauptnavigation
.status-badge
.dialog
```

Nicht:

```text
.myBox
.blueThing
.box2
.test
```

---

## 12.2 Schreibweise

Klassen verwenden:

```text
kebab-case
```

Beispiele:

```text
.seitenkopf-aktionen
.karte-inhalt
.status-badge
```

---

## 12.3 Komponentenstruktur

Es wird eine BEM-inspirierte Struktur verwendet:

```css
.karte {}
.karte__kopf {}
.karte__titel {}
.karte__inhalt {}
.karte__footer {}

.karte--warnung {}
.karte--kompakt {}
```

Die exakte BEM-Schreibweise ist empfohlen, aber die Lesbarkeit hat Vorrang.

---

## 12.4 Zustandsklassen

Zustände verwenden das Präfix:

```text
.ist-
```

Beispiele:

```text
.ist-aktiv
.ist-deaktiviert
.ist-geladen
.ist-offen
.ist-fehlerhaft
```

JavaScript-Hooks verwenden:

```text
.js-
```

Beispiele:

```text
.js-dialog-oeffnen
.js-navigation-toggle
```

JavaScript-Klassen dürfen nicht für Styling verwendet werden.

---

# 13. Selektoren

## 13.1 Erlaubt

```css
.button {}
.button--primaer {}
.karte__titel {}
[data-status="aktiv"] {}
```

---

## 13.2 Vermeiden

```css
main > div:nth-child(2) span {}
.sidebar ul li a.active {}
```

---

## 13.3 Maximale Verschachtelung

Maximal:

```text
2 Ebenen
```

Beispiel:

```css
.karte .status-badge {}
```

Besser ist häufig eine eigenständige Komponentenklasse.

---

# 14. `!important`

`!important` ist grundsätzlich verboten.

Ausnahmen:

- dokumentierte Accessibility-Hilfsklassen,
- klar definierte Utility-Klassen,
- zwingende Overrides externer Bibliotheken.

Jede Ausnahme muss kommentiert werden.

---

# 15. Layoutsystem

## 15.1 Flexbox

Flexbox wird verwendet für:

- Navigation,
- Toolbars,
- Buttongruppen,
- Kartenheader,
- horizontale Ausrichtung,
- kleine Layoutgruppen.

---

## 15.2 CSS Grid

Grid wird verwendet für:

- Dashboards,
- Formularraster,
- Kartenraster,
- Seitenlayouts,
- komplexe zweidimensionale Strukturen.

---

## 15.3 Kein Layout mit Floats

`float` wird nicht für Seitenlayouts verwendet.

Ausnahmen:

- bewusstes Umfließen von Bildern in längeren Texten.

---

# 16. Container

Standardcontainer:

```css
.inhaltscontainer {
    width: 100%;
    max-width: var(--breite-inhalt-max);
    margin-inline: auto;
    padding-inline: var(--abstand-8);
}
```

Responsive:

```css
@media (max-width: 1023px) {
    .inhaltscontainer {
        padding-inline: var(--abstand-6);
    }
}

@media (max-width: 639px) {
    .inhaltscontainer {
        padding-inline: var(--abstand-4);
    }
}
```

---

# 17. App-Shell

Beispiel:

```css
.app {
    min-height: 100vh;
    display: grid;
    grid-template-columns: var(--breite-sidebar) minmax(0, 1fr);
    grid-template-rows: var(--hoehe-header) minmax(0, 1fr);
}

.app__header {
    grid-column: 1 / -1;
}

.app__sidebar {
    grid-column: 1;
    grid-row: 2;
}

.app__inhalt {
    grid-column: 2;
    grid-row: 2;
    min-width: 0;
}
```

---

# 18. Sidebar-Zustände

```css
.app.ist-sidebar-kompakt {
    grid-template-columns:
        var(--breite-sidebar-kompakt)
        minmax(0, 1fr);
}
```

Keine festen Inhaltsbreiten, die beim Umschalten brechen.

---

# 19. Raster

## 19.1 Dashboard-Raster

```css
.dashboard-raster {
    display: grid;
    grid-template-columns: repeat(12, minmax(0, 1fr));
    gap: var(--abstand-6);
}
```

---

## 19.2 Kartenraster

```css
.kartenraster {
    display: grid;
    grid-template-columns: repeat(
        auto-fit,
        minmax(min(100%, 280px), 1fr)
    );
    gap: var(--abstand-6);
}
```

---

# 20. Buttons

Grundklasse:

```css
.button {
    min-height: 40px;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: var(--abstand-2);
    padding-inline: var(--abstand-4);
    border: 1px solid transparent;
    border-radius: var(--radius-standard);
    font-weight: 600;
    line-height: 1;
    transition:
        background-color var(--dauer-standard) var(--easing-standard),
        border-color var(--dauer-standard) var(--easing-standard),
        color var(--dauer-standard) var(--easing-standard),
        box-shadow var(--dauer-standard) var(--easing-standard);
}
```

---

## 20.1 Primärbutton

```css
.button--primaer {
    background: var(--farbe-primaer);
    color: #ffffff;
}

.button--primaer:hover {
    background: var(--farbe-primaer-hover);
}

.button--primaer:active {
    background: var(--farbe-primaer-aktiv);
}
```

---

## 20.2 Sekundärbutton

```css
.button--sekundaer {
    background: var(--farbe-flaeche);
    color: var(--farbe-text);
    border-color: var(--farbe-rahmen);
}
```

---

## 20.3 Gefahrenbutton

```css
.button--gefahr {
    background: var(--farbe-fehler);
    color: #ffffff;
}
```

---

## 20.4 Deaktivierte Buttons

```css
.button:disabled,
.button.ist-deaktiviert {
    opacity: 0.55;
    cursor: not-allowed;
    pointer-events: none;
}
```

---

# 21. Karten

```css
.karte {
    background: var(--farbe-flaeche);
    border: 1px solid var(--farbe-rahmen);
    border-radius: var(--radius-karte);
    box-shadow: var(--schatten-klein);
}

.karte__kopf,
.karte__inhalt,
.karte__footer {
    padding: var(--abstand-6);
}

.karte__kopf + .karte__inhalt,
.karte__inhalt + .karte__footer {
    border-top: 1px solid var(--farbe-rahmen);
}
```

---

# 22. Status-Badges

```css
.status-badge {
    min-height: 24px;
    display: inline-flex;
    align-items: center;
    gap: var(--abstand-1);
    padding-inline: var(--abstand-2);
    border-radius: var(--radius-rund);
    font-size: var(--schriftgroesse-1);
    font-weight: 600;
}
```

Varianten:

```text
.status-badge--neutral
.status-badge--info
.status-badge--erfolg
.status-badge--warnung
.status-badge--fehler
```

---

# 23. Fokuszustände

Alle interaktiven Elemente benötigen einen sichtbaren Fokuszustand.

```css
:focus-visible {
    outline: 3px solid color-mix(
        in srgb,
        var(--farbe-primaer) 35%,
        transparent
    );
    outline-offset: 2px;
}
```

Der Fokus darf nicht entfernt werden.

Nicht erlaubt:

```css
*:focus {
    outline: none;
}
```

---

# 24. Hover-Zustände

Hover-Zustände werden nur für Geräte angewendet, die Hover unterstützen.

```css
@media (hover: hover) {
    .karte--interaktiv:hover {
        border-color: var(--farbe-primaer);
        box-shadow: var(--schatten-standard);
    }
}
```

Dadurch werden unerwünschte Hover-Effekte auf Touchgeräten vermieden.

---

# 25. Responsive Breakpoints

Verbindliche Breakpoints:

```text
Mobil: bis 639px
Tablet: 640px bis 1023px
Desktop: 1024px bis 1439px
Großer Desktop: ab 1440px
```

Beispiele:

```css
@media (max-width: 639px) {}

@media (min-width: 640px) and (max-width: 1023px) {}

@media (min-width: 1024px) {}

@media (min-width: 1440px) {}
```

---

# 26. Mobile-First oder Desktop-First

Neue Komponenten werden bevorzugt Mobile-First entwickelt.

Beispiel:

```css
.seitenkopf {
    display: flex;
    flex-direction: column;
    gap: var(--abstand-4);
}

@media (min-width: 1024px) {
    .seitenkopf {
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
    }
}
```

---

# 27. Container Queries

Container Queries dürfen für wiederverwendbare Komponenten eingesetzt werden.

Beispiel:

```css
.widget {
    container-type: inline-size;
}

@container (min-width: 520px) {
    .widget__inhalt {
        grid-template-columns: 1fr 1fr;
    }
}
```

Container Queries sind besonders geeignet für Dashboard-Widgets.

---

# 28. Moderne CSS-Funktionen

Erlaubt und empfohlen:

```text
min()
max()
clamp()
calc()
color-mix()
aspect-ratio
object-fit
gap
logical properties
```

Beispiel:

```css
.seitentitel {
    font-size: clamp(24px, 2vw, 30px);
}
```

---

# 29. Logische Eigenschaften

Bevorzugt:

```css
margin-inline
margin-block
padding-inline
padding-block
border-inline-start
inset-inline-end
```

Statt ausschließlich:

```css
margin-left
margin-right
```

Dadurch bleibt die Oberfläche später leichter internationalisierbar.

---

# 30. Animationen

## 30.1 Grundregel

Animationen müssen einen funktionalen Zweck erfüllen.

---

## 30.2 Erlaubte Eigenschaften

Bevorzugt animieren:

```text
opacity
transform
background-color
border-color
box-shadow
```

Vermeiden:

```text
width
height
top
left
margin
```

wenn dadurch unnötige Layoutberechnungen entstehen.

---

## 30.3 Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
    *,
    *::before,
    *::after {
        scroll-behavior: auto !important;
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
    }
}
```

Diese Ausnahme darf `!important` verwenden.

---

# 31. Z-Index-System

Z-Index-Werte werden zentral definiert.

```css
:root {
    --z-basis: 1;
    --z-dropdown: 100;
    --z-sticky: 200;
    --z-sidebar-mobil: 300;
    --z-overlay: 400;
    --z-dialog: 500;
    --z-toast: 600;
    --z-tooltip: 700;
}
```

Keine zufälligen Werte wie:

```css
z-index: 999999;
```

---

# 32. Tooltips

Tooltips:

- nur für ergänzende Informationen,
- nicht für zwingend notwendige Inhalte,
- erscheinen bei Hover und Tastaturfokus,
- besitzen eine kurze Verzögerung,
- bleiben lesbar,
- überschreiten nicht die Bildschirmgrenzen.

---

# 33. Dialoge und Overlays

```css
.overlay {
    position: fixed;
    inset: 0;
    z-index: var(--z-overlay);
    display: grid;
    place-items: center;
    padding: var(--abstand-4);
    background: rgb(15 23 42 / 0.58);
    backdrop-filter: blur(4px);
}

.dialog {
    width: min(100%, 560px);
    max-height: calc(100vh - 32px);
    overflow: auto;
    background: var(--farbe-flaeche);
    border-radius: var(--radius-dialog);
    box-shadow: var(--schatten-dialog);
}
```

---

# 34. Sidepanels

```css
.sidepanel {
    position: fixed;
    inset-block: 0;
    inset-inline-end: 0;
    z-index: var(--z-dialog);
    width: min(100%, 480px);
    background: var(--farbe-flaeche);
    box-shadow: var(--schatten-gross);
}
```

---

# 35. Scrollbars

Eigene Scrollbar-Stile werden nur dezent eingesetzt.

Sie dürfen:

- nicht zu schmal,
- nicht zu kontrastarm,
- nicht vollständig versteckt

sein.

---

# 36. Textüberlauf

Einzeilige Inhalte:

```css
.text-kuerzen {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}
```

Mehrzeilige Inhalte:

```css
.text-zeilen-3 {
    display: -webkit-box;
    overflow: hidden;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 3;
}
```

Wichtige Informationen dürfen nicht ausschließlich abgeschnitten werden.

---

# 37. Technische Inhalte

Code, JSON und Systemwerte verwenden:

```css
.technischer-wert {
    font-family: var(--schrift-monospace);
    font-size: var(--schriftgroesse-2);
}
```

Lange technische Inhalte müssen umbrechen oder horizontal scrollbar sein.

---

# 38. Hilfsklassen

Erlaubte Hilfsklassen werden zentral definiert.

Beispiele:

```css
.sr-only {}
.versteckt {}
.text-mitte {}
.text-rechts {}
.flex {}
.flex-spalte {}
.flex-zwischen {}
.breite-voll {}
```

Nicht erlaubt:

Hunderte einmalig genutzte Hilfsklassen.

---

# 39. Screenreader-Klasse

```css
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
}
```

---

# 40. Druckansicht

```css
@media print {
    .app__header,
    .app__sidebar,
    .seitenkopf__aktionen,
    .toolbar,
    .button,
    .toast-container {
        display: none !important;
    }

    body {
        background: #ffffff;
        color: #000000;
    }

    .app {
        display: block;
    }
}
```

Die Druckansicht darf `!important` verwenden, wenn sie bewusst den Bildschirmstil überschreibt.

---

# 41. Externe CSS-Bibliotheken

Externe Bibliotheken dürfen nur verwendet werden, wenn:

- der Mehrwert klar ist,
- keine vorhandene Komponente die Aufgabe erfüllt,
- die Bibliothek aktiv gepflegt wird,
- sie sicher eingebunden werden kann,
- sie das Designsystem nicht überschreibt,
- sie keine unnötig große Abhängigkeit erzeugt.

Keine vollständigen UI-Frameworks ohne ausdrückliche Architekturentscheidung.

---

# 42. Kein unkontrolliertes CSS-Framework

Nicht automatisch einführen:

```text
Bootstrap
Tailwind CSS
Material UI
Bulma
Foundation
```

Eine spätere Verwendung ist nur zulässig, wenn sie ausdrücklich im Architekturkonzept beschlossen wird.

Das vorhandene Designsystem bleibt die führende Instanz.

---

# 43. Performance

## 43.1 Regeln

- Keine unnötig tiefen Selektoren.
- Keine riesigen Hintergrundbilder.
- Keine ungenutzten Frameworks.
- Keine duplizierten Komponentenstile.
- CSS-Dateien für Produktion bündeln und minimieren.
- Kritische Styles klein halten.
- Webfonts lokal und sparsam laden.
- Keine fünf Schriftschnitte laden, wenn zwei ausreichen.

---

## 43.2 Font Loading

Empfohlene Schnitte:

```text
400
500
600
700
```

`font-display: swap` verwenden.

---

# 44. Browserunterstützung

Das System richtet sich an aktuelle Versionen von:

```text
Chrome
Edge
Firefox
Safari
```

Sehr alte Browser werden nicht unterstützt.

Progressive Enhancement ist erlaubt.

Moderne Funktionen benötigen sinnvolle Fallbacks, wenn sie für die Kernbedienung relevant sind.

---

# 45. Kommentare

Kommentare erklären:

- warum eine Sonderregel notwendig ist,
- warum ein Browser-Fallback existiert,
- warum eine Abweichung vom Standard gewählt wurde.

Nicht:

```css
/* Setzt die Farbe auf Blau */
```

Sondern:

```css
/*
 * Höherer Kontrast als die Standard-Primärfarbe,
 * damit der aktive Navigationseintrag im Dark Mode lesbar bleibt.
 */
```

---

# 46. Reihenfolge innerhalb einer CSS-Regel

Empfohlene Reihenfolge:

```text
1. Positionierung
2. Box-Modell
3. Layout
4. Typografie
5. Darstellung
6. Animation
```

Beispiel:

```css
.karte {
    position: relative;

    width: 100%;
    padding: var(--abstand-6);

    display: flex;
    flex-direction: column;
    gap: var(--abstand-4);

    color: var(--farbe-text);

    background: var(--farbe-flaeche);
    border: 1px solid var(--farbe-rahmen);
    border-radius: var(--radius-karte);
    box-shadow: var(--schatten-klein);

    transition:
        border-color var(--dauer-standard) var(--easing-standard),
        box-shadow var(--dauer-standard) var(--easing-standard);
}
```

---

# 47. Verbotene Muster

Nicht erlaubt:

```text
Inline-Styles ohne technische Notwendigkeit
IDs als Styling-Selektoren
!important ohne dokumentierte Ausnahme
zufällige Z-Index-Werte
Farben außerhalb der Design Tokens
Abstände außerhalb des 4-Pixel-Rasters
mehr als zwei Selektor-Verschachtelungen
CSS für nur eine einzelne Seite ohne Wiederverwendungsprüfung
unklare Klassen wie .box1, .item2 oder .test
globale Elementregeln innerhalb von Modulen
JavaScript-Hook-Klassen als Stylingklassen
```

---

# 48. Qualitätsprüfung

Vor Abschluss einer neuen Komponente muss geprüft werden:

```text
[ ] Design Tokens verwendet
[ ] Keine fest codierten Farben
[ ] Keine unnötigen Inline-Styles
[ ] Keine ID-Selektoren
[ ] Spezifität niedrig
[ ] Light Mode geprüft
[ ] Dark Mode geprüft
[ ] Mobil geprüft
[ ] Tablet geprüft
[ ] Desktop geprüft
[ ] Tastaturfokus sichtbar
[ ] Reduced Motion berücksichtigt
[ ] Keine Layoutsprünge
[ ] Kein unnötiges !important
[ ] Keine doppelte CSS-Komponente
[ ] Klassen verständlich benannt
[ ] Hover nur für Hover-Geräte
[ ] Druckansicht bei relevanten Seiten geprüft
```

---

# 49. Beispiel einer vollständigen Komponente

HTML:

```html
<article class="karte karte--interaktiv">
    <header class="karte__kopf">
        <div>
            <h2 class="karte__titel">OpenClaw Server</h2>
            <p class="karte__beschreibung">
                Drei aktive Sub-Agenten
            </p>
        </div>

        <span class="status-badge status-badge--erfolg">
            Aktiv
        </span>
    </header>

    <div class="karte__inhalt">
        <dl class="datenliste">
            <div class="datenliste__zeile">
                <dt>Hostname</dt>
                <dd>openclaw-01</dd>
            </div>

            <div class="datenliste__zeile">
                <dt>IP-Adresse</dt>
                <dd class="technischer-wert">192.168.1.40</dd>
            </div>
        </dl>
    </div>

    <footer class="karte__footer">
        <button class="button button--sekundaer">
            Details
        </button>

        <button class="button button--primaer">
            Öffnen
        </button>
    </footer>
</article>
```

CSS:

```css
.karte--interaktiv {
    transition:
        border-color var(--dauer-standard) var(--easing-standard),
        box-shadow var(--dauer-standard) var(--easing-standard),
        transform var(--dauer-standard) var(--easing-standard);
}

@media (hover: hover) {
    .karte--interaktiv:hover {
        border-color: color-mix(
            in srgb,
            var(--farbe-primaer) 35%,
            var(--farbe-rahmen)
        );
        box-shadow: var(--schatten-standard);
        transform: translateY(-2px);
    }
}
```

---

# 50. Zusammenfassung

Das CSS der Anwendung folgt einem zentralen, tokenbasierten Designsystem.

Verbindliche Grundsätze:

- zentrale Design Tokens,
- niedrige Spezifität,
- Klassen statt IDs,
- keine Inline-Styles,
- kein unkontrolliertes CSS-Framework,
- Light und Dark Mode,
- Mobile-First,
- moderne Layouttechniken,
- sichtbare Fokuszustände,
- reduzierte Animationen,
- kontrollierte Z-Index-Ebenen,
- wiederverwendbare Komponenten.

Neue Module erweitern das bestehende CSS-System.

Sie dürfen kein paralleles oder abweichendes Styling-System erzeugen.