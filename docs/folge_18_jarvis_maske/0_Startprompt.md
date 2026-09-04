# Noob2Claw – Folge 18: Startprompt
# Futuristische Jarvis-Maske für Sprachgespräche

Du arbeitest am bestehenden Projekt Noob2Claw.

Erstelle einen neuen Navigationspunkt `Jarvis`. Dort sieht der Benutzer zunächst alle für ihn verfügbaren KI-Agenten und kann einen Agenten auswählen. Danach öffnet sich eine eigenständige, sehr hochwertige und stark animierte Sprachoberfläche, über die der Benutzer mit diesem Agenten sprechen kann.

Die Maske soll sich wie eine moderne Sprachzentrale aus einem hochwertigen Science-Fiction-Computerspiel anfühlen. Der bereitgestellte Entwurf ist nur eine grobe Richtungsreferenz und darf nicht einfach kopiert werden. Verwende das neue Noob2Claw-Designkonzept und die detaillierten Zustands- und Animationsregeln aus der Aufgabenbeschreibung.

Die Jarvis-Maske ist ein zusätzlicher Client für den vorhandenen Chat. Sie darf kein zweites Chatsystem und keine direkten Anbieteraufrufe erzeugen.

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

Aktualisiere die Vorlage vorsichtig und erhalte vorhandene lokale Arbeit. Lies anschließend vollständig:

- sämtliche Dokumente unter `docs/folge_9_framework/`,
- Folge 10 für Agentenverwaltung und Agentenbilder,
- Folge 14 für Integrationsverwaltung und globale Standards,
- Folge 15 und 16 für Speech-to-Text, Text-to-Speech und Stimmen,
- Folge 17 für Sprachaufnahme und Vorlesen im vorhandenen Chat,
- `docs/folge_18_jarvis_maske/1_Jarvis_Maske.md`,
- `docs/folge_18_jarvis_maske/2_UI_Designsystem_und_Animationen.md`.

Das externe Referenzvideo ist eine Inspirationsquelle, keine zusätzliche Arbeitsanweisung. Baue eine eigenständige Noob2Claw-Oberfläche und kopiere keine fremden Marken, Figuren oder Designs.

---

# 2. Aktiven Projektstand analysieren

Prüfe vor Änderungen mindestens:

```text
/mnt/noob2claw/index.php
/mnt/noob2claw/api.php
/mnt/noob2claw/inc/
/mnt/noob2claw/nav/
/mnt/noob2claw/js/
/mnt/noob2claw/css/
/mnt/noob2claw/uploads/
/mnt/noob2claw/sql/
```

Suche insbesondere nach:

- Navigation und Rechteprüfung,
- Agentenliste, Agentenbildern und Agentenberechtigungen,
- Chat-, Nachrichten- und Konversationsmodell,
- Nachrichtenversand, Streaming oder Polling,
- Speech-to-Text-Ablauf aus Folge 17,
- Text-to-Speech-Aufträgen und Wiedergabe aus Folge 17,
- globalen Integrationsstandards,
- Agenten-Stimmenzuordnung,
- Medien-, Datei- und Uploadverwaltung,
- zentraler JavaScript-Zustandsverwaltung,
- vorhandenen CSS-Variablen, Komponenten und responsiven Regeln,
- Cronjobs beziehungsweise Aufräumjobs,
- CSRF-, Rechte-, Logging- und Fehlerfunktionen.

Die neue Maske verwendet diese Funktionen. Erzeuge keine parallelen Agenten-, Chat-, Nachrichten-, Integrations-, Medien- oder Rechtearchitekturen.

Auch das visuelle System bleibt eine Erweiterung von Folge 9: Verwende deutsche,
projektbezogene CSS-Klassennamen und vorhandene zentrale Design-Tokens. Neue
`--farbe-jarvis-*`-Variablen sind nur für echte Jarvis-Spezialeffekte zulässig, müssen
von zentralen Tokens abgeleitet werden und dürfen kein zweites Theme bilden.

---

# 3. Pflichtziele

1. Neuen sichtbaren Navigationspunkt `Jarvis` ergänzen.
2. Zugriff über ein eigenes Recht absichern.
3. Übersicht aller für den Benutzer erlaubten aktiven Agenten anzeigen.
4. Agenten suchen und per Maus, Touch oder Tastatur auswählen können.
5. Nach Auswahl die futuristische Voice-Link-Ansicht öffnen.
6. Vorhandenen Chat des Agenten laden beziehungsweise nach bestehenden Regeln einen neuen Chat beginnen.
7. Sprachaufnahme per gedrückter Leertaste ermöglichen.
8. Alternativ einen großen Mikrofonbutton für Maus, Touch und Tastatur anbieten.
9. Aufnahme nach dem Stoppen über die vorhandene Speech-to-Text-Funktion transkribieren.
10. Erkannten Text sichtbar und bearbeitbar anzeigen.
11. Benutzer kann den Text bewusst absenden oder neu aufnehmen.
12. Nachricht über den vorhandenen Chatweg senden.
13. Agentenantwort im Transkript anzeigen.
14. Fertige Agentenantwort automatisch über die vorhandene Text-to-Speech-Funktion vorlesen.
15. Die in Folge 15 beziehungsweise 17 konfigurierte Stimme des Agenten verwenden.
16. Laufende Agentenstimme durch neue Spracheingabe stoppen können.
17. Alle Gesprächsphasen mit klaren Texten, Farben und Animationen darstellen.
18. Starkes, eigenständiges Science-Fiction-Design mit Agentenkern, Orbits, Waveform, Tiefeneffekten und HUD-Flächen umsetzen.
19. reduzierte Bewegung und sparsame Leistungsmodi anbieten.
20. Responsive Bedienung und Barrierefreiheit sicherstellen.
21. Geheimnisse, Audiodateien, Chats und Nachrichten weiterhin serverseitig schützen.
22. Normalen Chat aus Folge 17 unverändert funktionsfähig lassen.

---

# 4. Neuer Navigationspunkt

Sichtbarer Name:

```text
Jarvis
```

Technischer Schlüssel beziehungsweise Route nach Projektkonvention, sinngemäß:

```text
jarvis
```

Passendes Recht, sinngemäß:

```text
jarvis_nutzen
```

Regeln:

- Navigation nur bei Berechtigung anzeigen.
- Route selbst erneut serverseitig schützen.
- Administratoren erhalten das Recht idempotent.
- Andere Rollen erhalten es nicht automatisch.
- Direkter URL-Aufruf ohne Recht wird abgewiesen.
- Benutzer sieht nur Agenten, die er auch im normalen Chat verwenden darf.

---

# 5. Zwei Ansichten

## 5.1 Agenten-Lobby

Beim Öffnen von `Jarvis` erscheint eine vollwertige Agentenübersicht.

Jede Agentenkarte zeigt:

- Agentenbild beziehungsweise sicheren Fallback,
- Agentenname,
- kurze vorhandene Beschreibung oder Rolle,
- Status der Sprachbereitschaft,
- sichtbare Markierung des zuletzt verwendeten Agenten,
- Aktion `Gespräch starten`.

Sprachbereitschaft darf nur echte Konfiguration abbilden:

```text
Bereit
Speech-to-Text fehlt
Text-to-Speech fehlt
Stimme fehlt oder ungültig
nur Text verfügbar
```

Keine erfundenen Online-, Latenz- oder Modellwerte anzeigen.

Bei vielen Agenten:

- Suchfeld,
- sinnvolle Sortierung nach Name oder letzter Nutzung,
- keine unendliche unperformante Partikelanimation pro Karte,
- Tastaturnavigation und sichtbarer Fokus.

## 5.2 Voice Link

Nach Auswahl öffnet sich die Gesprächsansicht mit:

- oberer Systemleiste,
- kompaktem Agenten-Dock,
- zentralem Agentenkern,
- großem Gesprächsstatus,
- Audio-Waveform und Pegel,
- Push-to-Talk-Steuerung,
- Transkriptpanel,
- Texteingabe als Fallback,
- Lautsprecher- und Stopsteuerung,
- Aktion `Gespräch beenden`.

Die Ansicht darf sich bildschirmfüllend anfühlen, muss aber innerhalb der vorhandenen Anwendung und Rechtearchitektur funktionieren.

---

# 6. Auswahl und Chatbindung

Beim Agentenwechsel beziehungsweise Gesprächsstart:

1. Agenten-ID serverseitig prüfen.
2. Benutzerberechtigung für diesen Agenten prüfen.
3. vorhandenes Chat- beziehungsweise Konversationsmodell verwenden.
4. zuletzt verwendeten erlaubten Chat öffnen, falls dies dem aktuellen Projektverhalten entspricht.
5. einen neuen Chat erst nach vorhandener Projektkonvention anlegen, möglichst erst bei der ersten Nachricht.
6. vorhandene Nachrichten sicher in das Transkript laden.
7. nur freigegebene Nachrichten und Dateien anzeigen.

Wenn ein ungesendeter Entwurf oder eine laufende Aufnahme existiert, darf ein Agentenwechsel nicht stillschweigend Daten verwerfen. Biete `Beim Agenten bleiben`, `Entwurf verwerfen und wechseln` und – wenn technisch passend – `Entwurf behalten` an.

`Gespräch beenden`:

- stoppt Aufnahme und Wiedergabe,
- leert flüchtige Audiowarteschlangen,
- behandelt ungesendete Entwürfe nach Bestätigung,
- schließt AudioContext und Listener,
- kehrt zur Agenten-Lobby zurück,
- löscht nicht automatisch den gespeicherten Chatverlauf.

---

# 7. Gesprächszustandsmaschine

Verwende eine zentrale Zustandsmaschine statt vieler unabhängiger Boolean-Werte.

Pflichtzustände:

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

Die Zustandsmaschine steuert:

- sichtbaren Hauptstatus,
- erlaubte Aktionen,
- Mikrofon- und Tastaturverhalten,
- Animationsprofil,
- Statusfarbe,
- Screenreader-Text,
- laufende Requests,
- Audioaufnahme und Wiedergabe.

Ungültige Übergänge werden verhindert. Beispiel: Während `TRANSCRIBING` darf nicht unkontrolliert eine zweite Aufnahme derselben Session gestartet werden.

Dokumentiere die erlaubten Übergänge zentral und teste sie.

---

# 8. Push-to-Talk per Leertaste

Desktop-Verhalten:

```text
Leertaste drücken und halten
→ Aufnahme beginnt

Leertaste loslassen
→ Aufnahme endet
→ Transkription beginnt
```

Regeln:

- `keydown` und `keyup` verwenden.
- Space über `event.code === 'Space'` beziehungsweise kompatible Prüfung erkennen.
- wiederholte `keydown`-Ereignisse nicht als neue Aufnahme behandeln.
- nur ohne unerwünschte Modifikatortasten reagieren.
- Leertaste nicht abfangen, wenn Fokus in `input`, `textarea`, `select`, `button`, Link oder `contenteditable` liegt.
- nur in erlaubten Zuständen starten.
- während der Aufnahme Default-Scrolling gezielt verhindern.
- bei `keyup` genau einmal stoppen.
- bei `window.blur`, `document.hidden`, `Escape`, Gesprächsende oder Fehler sicher stoppen beziehungsweise verwerfen.
- verlorenes `keyup` darf keine endlose Aufnahme erzeugen.
- maximale Dauer bleibt server- und clientseitig begrenzt.

Wenn der Agent gerade spricht, darf Push-to-Talk die Wiedergabe stoppen und anschließend die neue Aufnahme beginnen. Dieses Unterbrechen wird sichtbar als `Agent gestoppt – ich höre zu` dargestellt.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key
- https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/repeat
- https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API
[/Sources]

---

# 9. Mikrofonbutton

Der große zentrale Mikrofonbutton ist die gleichwertige Alternative zur Leertaste.

Standardbedienung:

```text
erster Klick oder Tap
→ Aufnahme starten

zweiter Klick oder Tap
→ Aufnahme stoppen und transkribieren
```

Dies ist für Touch, Sprachsteuerung und motorische Barrierefreiheit verständlicher als ausschließliches Gedrückthalten.

Optional darf eine Einstellung `Button zum Sprechen halten` ergänzt werden. Wenn sie umgesetzt wird:

- Pointer Events statt getrennte Maus- und Touchlogik verwenden,
- `setPointerCapture()` gezielt einsetzen,
- `pointerup`, `pointercancel` und `lostpointercapture` behandeln,
- bei jedem Abbruch sicher stoppen,
- Klickmodus bleibt als zugängliche Alternative verfügbar.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events
[/Sources]

---

# 10. Aufnahme und Transkription

Verwende exakt den sicheren Speech-to-Text-Ablauf aus Folge 17:

- `getUserMedia()` erst nach Benutzeraktion,
- `MediaRecorder` nach Feature-Erkennung,
- unterstützten MIME-Typ ermitteln,
- sichtbarer Timer und Pegel,
- leere Aufnahme verwerfen,
- Tracks zuverlässig stoppen,
- Audio an den eigenen Server senden,
- dort Rechte, Inhalt, Typ, Größe und Dauer prüfen,
- globalen `speech_to_text`-Standard auflösen,
- Integration über zentralen Fähigkeitsdienst aufrufen,
- temporäre Aufnahme nach Aufbewahrungsregel löschen.

In der Jarvis-Maske startet die Transkription nach einem gültigen Aufnahmestopp automatisch. Das Ergebnis wird jedoch nicht automatisch als Nachricht gesendet.

Wenn die Integration kein Streaming-STT unterstützt, zeigt die Oberfläche während der Aufnahme keine erfundene Live-Transkription. Sie zeigt Waveform, Pegel und `Ich höre zu`. Der Text erscheint nach dem Zustand `TRANSCRIBING`.

---

# 11. Text prüfen und absenden

Nach erfolgreicher Transkription wechselt die Maske in `REVIEW`.

Anzeigen:

- erkannten Text prominent unter dem Agentenkern und im Composer,
- Aktion `Senden`,
- Aktion `Neu aufnehmen`,
- normale Bearbeitung per Tastatur,
- Zeichenlimit und Validierung,
- klaren Hinweis auf leeres oder unsicheres Ergebnis.

Senden:

- erst nach bewusster Benutzeraktion,
- verwendet den vorhandenen Chat-Nachrichtenendpunkt,
- legt keine besondere Audio-Nachricht an,
- verhindert Doppelklick und doppeltes Senden,
- übergibt nur den bestätigten Text,
- behandelt bestehende Fehlerzustände des Chats.

Tastatur:

- `Enter` sendet nur nach bestehender Chatkonvention,
- `Shift+Enter` erzeugt eine neue Zeile, falls der vorhandene Composer dies unterstützt,
- Leertasten-Push-to-Talk ist deaktiviert, solange das Texteingabefeld fokussiert ist.

---

# 12. Agentenantwort und Text-to-Speech

Nach dem Senden:

1. Zustand `AGENT_THINKING` anzeigen.
2. vorhandenes Streaming oder Polling verwenden.
3. Antwort fortlaufend sicher im Transkript anzeigen.
4. TTS erst nach endgültigem Nachrichtenabschluss anfordern.
5. vorhandenen Sprechtext-Konverter aus Folge 17 verwenden.
6. globalen `text_to_speech`-Standard auflösen.
7. gültige Stimme des Agenten für genau diesen Integrationseintrag bestimmen.
8. TTS-Auftrag idempotent erzeugen oder wiederverwenden.
9. geschützte Audiodatei abspielen.
10. Zustand `AGENT_SPEAKING` darstellen.

Die Jarvis-Session ist ausdrücklich eine Sprachansicht. Der Lautsprecher ist nach bewusstem Gesprächsstart aktiv, kann aber jederzeit stummgeschaltet oder gestoppt werden.

Autoplay-Blockierung wird wie in Folge 17 behandelt:

- `play()`-Promise auswerten,
- bei Blockierung `Audio bereit – zum Abspielen klicken` anzeigen,
- manuellen Abspielbutton anbieten,
- Fehler der Wiedergabe nicht als Fehler der Agentenantwort darstellen.

---

# 13. Stimme des Agenten

Verwende ausschließlich die bestehende Agenten-Stimmenzuordnung.

Auflösungsreihenfolge:

```text
gültige Agentenstimme für aktuellen TTS-Eintrag
        ↓ sonst
ausdrücklich konfigurierte Standardstimme des TTS-Eintrags
        ↓ sonst
Textantwort anzeigen und klar melden, dass keine Stimme verfügbar ist
```

Der Chat funktioniert auch dann weiter, wenn TTS oder Stimme fehlen. Die Antwort bleibt immer als Text sichtbar.

Agenten-Dock und Lobby zeigen einen kleinen echten Konfigurationsstatus. Eine fehlende Stimme wird nicht als „offline“ bezeichnet.

---

# 14. Unterbrechen des Agenten

Wenn `AGENT_SPEAKING` aktiv ist und der Benutzer:

- die Leertaste drückt,
- den Mikrofonbutton startet,
- den Stopbutton verwendet,

dann:

1. aktuelle lokale Audiowiedergabe sofort stoppen,
2. automatische Wiedergabewarteschlange der Session behandeln,
3. bei Push-to-Talk danach Aufnahme starten,
4. bereits gespeicherte Agentenantwort nicht löschen,
5. keine Anbieter-Abbruchfunktion vortäuschen, wenn nur die lokale Wiedergabe gestoppt wurde.

Das Unterbrechen betrifft in dieser Folge die Sprachausgabe. Ob ein noch laufender Agentenprozess serverseitig abgebrochen werden kann, richtet sich ausschließlich nach der vorhandenen Chatarchitektur.

---

# 15. Transkriptpanel

Das rechte Panel zeigt den vorhandenen Chatverlauf:

- sichere Benutzer- und Agentennachrichten,
- Agentenbild beziehungsweise Initialen,
- Name und echte Zeitangabe,
- Status laufender Agentenantworten,
- aktueller Transkriptionsentwurf,
- Fehler mit gezielter Wiederholen-Aktion,
- Textinput als Fallback,
- Senden-Button.

Scrollregeln:

- automatisch nach unten nur, wenn Benutzer bereits am Ende ist,
- sonst Hinweis `Neue Nachricht`,
- Fokus und Auswahl des Benutzers nicht durch neue Tokens zerstören,
- alte Nachrichten paginiert oder nach vorhandener Logik laden,
- TTS startet nicht erneut nur weil eine alte Nachricht gerendert wurde.

Interne Toolausgaben, Systemtexte und versteckte Metadaten werden nicht angezeigt oder vorgelesen.

---

# 16. Visuelles Design

Setze die Vorgaben aus `2_UI_Designsystem_und_Animationen.md` um.

Zwingende Merkmale:

- tiefer Midnight-Navy- bis Schwarz-Hintergrund,
- eigenständiges `NOOB2CLAW // VOICE LINK`-Branding,
- starke, aber lesbare Cyan-, Violett- und Magenta-Energieeffekte,
- Grün nur für Erfolg beziehungsweise echte Bereitschaft,
- Amber für Verarbeitung und Warnungen,
- Rot für Gesprächsende und schwere Fehler,
- zentraler Agentenavatar in mehrschichtigem Energiekern,
- Orbitlinien und Wellen mit echter Zustandsbedeutung,
- Agenten-Dock links,
- Transkript rechts,
- großer Mikrofon-Controller unten beziehungsweise zentral,
- klare Statuswörter,
- hochwertige Glasflächen und Tiefenwirkung,
- keine unlesbaren erfundenen Mikrodaten,
- keine fremden Marken oder geschützten Figuren.

Nutze vorhandene Agentenbilder. Fehlt ein Bild, verwende einen sauberen generischen Avatar aus dem bestehenden System, keine extern nachgeladene Zufallsgrafik.

---

# 17. Animationen mit Bedeutung

Mindestens folgende Zustandsprofile:

```text
READY
langsamer Cyan-Grundpuls

LISTENING
Audiopegel steuert Waveform und begrenzte Halo-Ausdehnung

TRANSCRIBING
amberfarbener kontrollierter Scanring

AGENT_THINKING
versetzte violette Orbitbewegung

VOICE_GENERATING
aufbauende Spektrallinie

AGENT_SPEAKING
Ausgangswaveform in Cyan und Magenta

ERROR
ruhige Warnkante und klare Handlung, kein hektisches Flackern
```

Zustandswechsel erfolgen weich und brechen laufende veraltete Animationen kontrolliert ab.

Die Maske darf spektakulär wirken, ohne den Inhalt hinter Effekten zu verstecken.

---

# 18. Audioanalyse

Für die lokale Visualisierung darf der vorhandene Mikrofonstream mit Web Audio und `AnalyserNode` ausgewertet werden.

Anforderungen:

- nur Pegel- und Zeitbereichsdaten für Anzeige,
- keine zusätzliche Speicherung,
- keine zusätzliche Übertragung,
- Canvas oder eine kleine feste Zahl von Elementen,
- Updates mit `requestAnimationFrame()`,
- Animation bei verborgener Seite pausieren,
- AudioContext beim Verlassen schließen,
- Zugriff nur während erforderlicher Audiozustände.

Die fachliche Aufnahme und STT-Datei bleiben die Quelle der Transkription. Die Visualisierung ersetzt keine Eingabevalidierung.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode
- https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame
- https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API
[/Sources]

---

# 19. Leistung

Implementiere mindestens die Modi:

```text
Standard
Sparsam
Effekte reduziert
```

Optional `Hoch`, wenn die vorhandene Zielumgebung es sinnvoll unterstützt.

Regeln:

- CSS bevorzugt über `transform` und `opacity` animieren,
- Audio-Waveform in einem Canvas statt vielen DOM-Knoten,
- Partikelanzahl begrenzen,
- Gerätepixelverhältnis des Canvas nach oben begrenzen,
- `requestAnimationFrame()` zeitbasiert verwenden,
- versteckte Seite pausiert visuelle Schleifen,
- keine Timer oder Listener nach Verlassen der Maske,
- responsive Ansichten reduzieren sekundäre Effekte,
- normale Chat- und Audiofunktionen dürfen bei ausgeschalteten Effekten nicht beeinträchtigt sein.

Zeige in der Oberfläche keine erfundene Bildrate. Verwende echte Messwerte nur, wenn sie wirklich erhoben werden.

---

# 20. Reduzierte Bewegung und Licht

Respektiere automatisch:

```css
@media (prefers-reduced-motion: reduce)
```

Im reduzierten Modus:

- keine Partikelbewegung,
- keine Parallax- oder Kamerabewegung,
- Orbits statisch,
- Status über Text, Farbe, Symbol und sanfte Opacity,
- einfache Pegelanzeige statt stark bewegter Waveform,
- alle Funktionen bleiben verfügbar.

Vermeide Stroboskop, schnelle großflächige Helligkeitswechsel und rote Blitzfolgen vollständig. Alarmzustände verwenden statische Kontur, Symbol und Text.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@media/prefers-reduced-motion
- https://www.w3.org/WAI/WCAG21/Techniques/general/G19
[/Sources]

---

# 21. Responsive Verhalten

Desktop:

- drei sichtbare Bereiche,
- Agentenkern dominant,
- Transkript dauerhaft geöffnet.

Tablet:

- Agenten-Dock kompakt,
- Transkript einblendbar,
- Push-to-Talk dauerhaft erreichbar.

Mobil:

- Agentenwahl als Overlay oder horizontaler Streifen,
- Transkript als Bottom Sheet,
- großer Mikrofonbutton im unteren Daumenbereich,
- keine winzige skalierte Desktopansicht,
- Leertastenhinweis ausblenden, wenn keine Hardwaretastatur sinnvoll ist,
- Klick-/Tap-Modus vollständig funktionsfähig.

Teste Quer- und Hochformat sowie Zoom bis 200 Prozent.

---

# 22. Barrierefreiheit

- semantische Buttons statt klickbarer `div`-Elemente,
- sichtbare Fokusrahmen,
- nachvollziehbare Tab-Reihenfolge,
- Agentenliste als echte Liste beziehungsweise Auswahlstruktur,
- Statusänderungen über passende `aria-live`-Region,
- Aufnahmezustand nicht nur über Farbe,
- Timer nicht jede Sekunde aggressiv an Screenreader melden,
- alle Funktionen ohne Maus nutzbar,
- Mikrofonbutton besitzt eindeutigen Namen passend zum Zustand,
- Dialoge verwalten Fokus korrekt,
- Texttranskript bleibt unabhängig vom Audio verfügbar,
- Lautsprecher lässt sich jederzeit stoppen,
- reduzierte Bewegung wird respektiert,
- Kontrast der Texte und Fokusindikatoren prüfen.

Die Leertaste darf nicht die normale Bedienung fokussierter Buttons oder Eingabefelder zerstören.

---

# 23. Seite verlassen und Sichtbarkeit

Bei `visibilitychange`, `pagehide`, Fensterverlust oder Route-Wechsel:

- laufende Aufnahme sicher stoppen oder verwerfen,
- Mikrofonspuren beenden,
- Agenten-Audio pausieren beziehungsweise stoppen,
- Push-to-Talk-Status zurücksetzen,
- visuelle Animationsschleifen pausieren,
- flüchtigen Textentwurf erhalten, wenn dies sicher möglich ist,
- keine automatische Wiedergabe beim bloßen Zurückkehren starten,
- keine stillen Hintergrundaufnahmen fortsetzen.

Der Serverzustand einer bereits gesendeten Nachricht bleibt unabhängig von der Darstellung korrekt.

---

# 24. Sicherheit und Datenschutz

Verbindlich:

- kein API-Key im Browser,
- keine direkten Anbieteraufrufe,
- Session-, CSRF- und Rechteprüfung,
- Agent-, Chat-, Nachrichten- und Dateiobjektberechtigung,
- Audiodateien nur über geschützte Route,
- temporäre Aufnahmen nach kurzer Frist löschen,
- keine vollständigen Audioinhalte oder Transkripte in allgemeinen Logs,
- serverseitige Limits für Größe, Dauer und Frequenz,
- sichere Ausgabe von Nachrichten und Fehlern,
- keine unkontrollierten externen Bild- oder Skriptquellen,
- Content Security Policy und bestehende Header nicht schwächen,
- Agentenbilder nur aus erlaubter vorhandener Dateiverwaltung,
- keine Anbieter-Rohantwort im Transkript.

Die Oberfläche zeigt verständlich, welche globalen Integrationen Sprache erkennen und erzeugen.

---

# 25. Datenmodell

Verwende vorhandene Tabellen aus Folge 17 für:

- Chats und Nachrichten,
- TTS-Aufträge und Audiodateien,
- Agentenstimmen,
- globale Integrationsstandards,
- temporäre STT-Dateien,
- benutzerbezogene Vorleseeinstellungen.

Optional erforderliche UI-Präferenzen, sinngemäß:

```text
benutzer_id
jarvis_letzter_agent_id
jarvis_effektmodus
jarvis_lautsprecher_aktiv
jarvis_lautstaerke
geaendert_am
```

Prüfe vor einer neuen Tabelle, ob das vorhandene Einstellungssystem diese Werte bereits speichern kann.

Keine Nachrichten duplizieren, nur weil sie in zwei Oberflächen angezeigt werden.

---

# 26. API-Nutzung

Die Jarvis-Maske nutzt vorhandene Aktionen, sinngemäß:

```text
Agenten auflisten
Chat laden
chat_audio_transkribieren
Chatnachricht senden
Agentenantwort abrufen beziehungsweise streamen
chat_tts_anfordern
chat_tts_status
geschützte Audiodatei ausliefern
```

Nur wenn eine notwendige UI-Präferenz nicht vorhanden ist, ergänze eine kleine idempotente Aktion.

Der Server bestimmt selbst:

- erlaubten Agenten,
- Chatzugehörigkeit,
- aktuellen STT- und TTS-Eintrag,
- Agentenstimme,
- Sprechtext,
- Dateizugriff.

Diese Werte werden nicht ungeprüft aus dem Browser übernommen.

---

# 27. Fehlerzustände

Mindestens behandeln:

```text
keine Agenten verfügbar
Agent nicht berechtigt oder inaktiv
Chat nicht erreichbar
Mikrofon nicht erlaubt
Aufnahmeformat nicht unterstützt
Aufnahme leer oder zu lang
Speech-to-Text nicht konfiguriert
Transkription leer oder fehlgeschlagen
Nachricht konnte nicht gesendet werden
Agentenantwort fehlgeschlagen
Text-to-Speech nicht konfiguriert
Agentenstimme ungültig
Audiogenerierung fehlgeschlagen
Autoplay blockiert
Verbindung unterbrochen
Gespräch während laufender Aktion beendet
```

Bei TTS-Fehler bleibt die Textantwort vollständig nutzbar. Bei STT-Fehler bleibt die Texteingabe als Fallback verfügbar.

Fehler reduzieren Effekte und zeigen eine klare nächste Handlung.

---

# 28. Testfälle

## Agenten-Lobby

- alle und nur erlaubte aktive Agenten erscheinen,
- keine Agenten ergibt hilfreichen Leerzustand,
- Suche, Fokus und Tastaturauswahl funktionieren,
- fehlende STT-, TTS- oder Stimmenkonfiguration wird korrekt dargestellt,
- Auswahl lädt richtigen vorhandenen Chat,
- Agentenwechsel mit Entwurf verlangt Entscheidung.

## Leertaste

- `keydown` startet genau eine Aufnahme,
- wiederholtes `keydown` startet keine weitere,
- `keyup` stoppt genau einmal,
- Fokus im Texteingabefeld verhindert Push-to-Talk,
- Seitenwechsel, `blur`, `Escape` und verlorener Fokus beenden sicher,
- maximale Aufnahmedauer greift,
- Leertaste während Agentensprache stoppt Audio und startet Aufnahme.

## Mikrofonbutton

- Klick/Tippen startet und stoppt,
- Tastaturaktivierung funktioniert,
- Pointer-Abbruch verursacht keine Daueraufnahme,
- sichtbarer und zugänglicher Zustand stimmen überein,
- Button ist auf Touchgeräten ausreichend groß.

## Gespräch

- Aufnahme wird transkribiert,
- Text erscheint erst nach erfolgreichem STT,
- kein falscher Hinweis auf Live-Transkription,
- Text kann geändert, verworfen und gesendet werden,
- vorhandener Chatweg speichert genau eine Nachricht,
- Agentenantwort streamt beziehungsweise lädt korrekt,
- TTS startet erst nach Abschluss,
- korrekte Agentenstimme wird verwendet,
- Autoplay-Fallback funktioniert,
- Stop und Unterbrechen funktionieren,
- Gesprächsende löscht den Chat nicht.

## Animation und Leistung

- jeder Zustand besitzt passendes Animationsprofil,
- Zustandswechsel hinterlassen keine alten Schleifen,
- reduzierte Bewegung funktioniert,
- Sparmodus funktioniert,
- versteckte Seite pausiert Animation und Mikrofon,
- keine schnellen Lichtblitze,
- lange Transkripte bleiben flüssig scrollbar,
- responsive Layouts funktionieren,
- Browserkonsole bleibt fehlerfrei.

## Sicherheit und Regression

- direkter URL-Aufruf ohne Recht wird abgewiesen,
- fremde Agenten-, Chat-, Nachrichten- und Datei-IDs werden abgewiesen,
- kein Secret in HTML, JavaScript, Netzwerkantwort oder Log,
- normaler Chat aus Folge 17 funktioniert unverändert,
- Integrationstestwerkzeug funktioniert weiter,
- Grok und OpenAI bleiben austauschbar,
- Agentenverwaltung und Stimmenauswahl funktionieren weiter.

---

# 29. Ausdrücklich nicht Bestandteil dieser Folge

- dauerhaft offenes Mikrofon,
- Wake Word,
- heimliche Hintergrundaufnahme,
- Echtzeit-Streaming-STT, sofern der bestehende Vertrag dies nicht bereits anbietet,
- echte bidirektionale Voice-to-Voice-Streamingverbindung,
- Lippen-Synchronisation,
- 3D-Spielengine oder WebGL-Zwang,
- Gruppenchat in der Jarvis-Maske,
- Avatargenerierung,
- neue Anbieterintegrationen,
- Klonen von Stimmen,
- Löschen von Chatverläufen beim Gesprächsende,
- Kopie einer geschützten Film-, Spiele- oder Social-Media-Oberfläche.

Bereite saubere Erweiterungspunkte vor, implementiere diese Themen aber nicht vorzeitig.

---

# 30. Entwicklungsreihenfolge

1. vorhandene Navigation, Agentenrechte und Chatarchitektur analysieren.
2. wiederverwendbare Aktionen aus Folge 17 identifizieren.
3. Route, Recht und Agenten-Lobby ergänzen.
4. Voice-Link-Grundlayout ohne Effekte funktionsfähig erstellen.
5. zentrale Gesprächszustandsmaschine implementieren.
6. Leertasten- und Mikrofonbutton-Steuerung anbinden.
7. STT, Review und vorhandenen Nachrichtenversand integrieren.
8. Agentenantwort und vorhandenen TTS-Ablauf integrieren.
9. Agentenwechsel, Unterbrechen und Gesprächsende robust machen.
10. visuelles Designsystem und zustandsgebundene Animationen ergänzen.
11. Audioanalyse und Canvas-Waveform integrieren.
12. reduzierte Bewegung, Sparmodus und Responsive Layout umsetzen.
13. Rechte, Datenschutz, Limits und Aufräumen prüfen.
14. Funktions-, Browser-, Leistungs-, Barrierefreiheits- und Regressionstests durchführen.

Baue zuerst den vollständigen funktionalen Ablauf. Ergänze danach die starken visuellen Effekte, ohne die Zustandslogik zu duplizieren.

---

# 31. Abschlussbericht

Dokumentiere:

- wiederverwendete Strukturen aus Folge 17,
- neue Route und neues Recht,
- Agenten-Lobby und Auswahlregeln,
- Chatbindung,
- Gesprächszustandsmaschine,
- Leertasten- und Buttonsteuerung,
- STT-, Review- und Sendeablauf,
- Agentenantwort und TTS-Ablauf,
- Auflösung der Agentenstimme,
- Unterbrechen und Gesprächsende,
- visuelles Designsystem,
- Canvas-, Audio- und Animationstechnik,
- reduzierte Bewegung und Leistungsmodi,
- Responsive Verhalten,
- Rechte, Datenschutz und Limits,
- neue und geänderte Dateien,
- Migrationen,
- ausgeführte Tests und Ergebnisse,
- bewusst nicht umgesetzte spätere Funktionen.

Die Aufgabe ist abgeschlossen, wenn Benutzer alle erlaubten Agenten in einer hochwertigen Übersicht auswählen, in einer deutlich animierten Jarvis-Maske per Leertaste oder Mikrofonbutton sprechen, den erkannten Text prüfen und senden, die Agentenantwort mit der korrekten Stimme hören und jederzeit sicher stoppen oder die Session beenden können – ohne ein zweites Chatsystem oder eine Anbieterabhängigkeit zu erzeugen.
