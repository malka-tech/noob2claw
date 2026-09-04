# Noob2Claw – Folge 18: Jarvis-Maske

In Folge 18 erhält Noob2Claw einen neuen Navigationspunkt `Jarvis`. Dort werden alle verfügbaren KI-Agenten als visuelle Auswahl angezeigt. Nach der Auswahl öffnet sich eine eigenständige, futuristische Gesprächsoberfläche für eine sprachgeführte Unterhaltung mit dem Agenten.

Die Maske verwendet die in Folge 17 fertiggestellten Chat-, Speech-to-Text- und Text-to-Speech-Funktionen. Sie ist eine neue, besonders inszenierte Oberfläche über denselben Diensten – kein zweites Chatsystem.

# Neues Designkonzept


Das Konzept wurde anhand des bereitgestellten Entwurfs neu gestaltet. Die Inspiration wird nicht pixelgenau kopiert. Die neue Fassung besitzt eine eigenständige Noob2Claw-Identität mit:

- Agenten-Dock mit direkter Auswahl,
- holografischem Agentenkern,
- reaktiven Energieringen und Audiowellen,
- klaren Zuständen für Zuhören, Verarbeiten, Denken, Sprechen und Fehler,
- Push-to-Talk per Leertaste,
- großem Mikrofon-Controller,
- fortlaufendem Transkript,
- reduzierter Bewegung für barrierearme Nutzung,
- Performance-Modi für schwächere Geräte.


# Bedienung

```text
Jarvis öffnen
      ↓
Agent aus der Übersicht wählen
      ↓
Leertaste halten oder Mikrofonbutton verwenden
      ↓
Aufnahme beenden
      ↓
Speech-to-Text erzeugt einen Textentwurf
      ↓
Text prüfen und absenden
      ↓
Agent antwortet im vorhandenen Chat
      ↓
Text-to-Speech liest mit der Agentenstimme vor
```

# Wichtige Architekturregel

Die Jarvis-Maske verwendet ausschließlich bestehende zentrale Funktionen:

- Agentenverwaltung,
- Chat und Nachrichten,
- `speech_to_text`,
- `text_to_speech`,
- globale Integrationsstandards,
- Agentenstimme,
- geschützte Medienablage,
- Rechte und Protokollierung.

Grok, OpenAI oder andere Anbieter werden nicht direkt aus der Maske angesprochen.

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
- `1_Jarvis_Maske.md` – fachliche und technische Spezifikation
- `2_UI_Designsystem_und_Animationen.md` – visuelles System, Komponenten, Zustände und Animationen

# Inspirationsquelle

- Referenzvideo: https://www.tiktok.com/@moritz.maaker/video/7655056688579349782?q=jarvis&t=1788439119386

Das Referenzvideo dient nur als Inspiration für Stimmung und Bedienidee. Die umzusetzende Oberfläche erhält eine eigene Gestaltung und verwendet keine fremden Marken, Figuren oder geschützten UI-Elemente.

# Browser- und Barrierefreiheitsgrundlagen

- Tastatureingaben: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key
- Pointer Events: https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events
- Audioanalyse: https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode
- Seitenstatus: https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API
- Reduzierte Bewegung: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@media/prefers-reduced-motion
- Begrenzung schneller Lichtblitze: https://www.w3.org/WAI/WCAG21/Techniques/general/G19

# Ergebnis

Nach Folge 18 besitzt Noob2Claw eine auffällige, spielerisch inszenierte Sprachzentrale. Benutzer wählen einen Agenten, sprechen per Leertaste oder Mikrofonbutton, prüfen das Transkript und senden es ab. Die Antwort erscheint im Transkript und wird mit der für den Agenten hinterlegten Stimme vorgelesen.
