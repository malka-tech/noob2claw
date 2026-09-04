# Noob2Claw – Folge 16: OpenAI aus der vorhandenen Grok-Integration ableiten

In Folge 16 wird nicht noch einmal dieselbe Architektur beschrieben. Stattdessen wird praktisch bewiesen, dass die in Folge 14 und 15 geschaffene Integrationsarchitektur wiederverwendbar ist.

Der KI-Agent erhält deshalb bewusst nur einen einzigen Arbeitsauftrag:

```text
Nutze die vorhandene Grok-Integration als Vorlage und baue daraus eine zusätzliche OpenAI-Integration.
```

Die vorhandene Grok-Integration bleibt erhalten. OpenAI kommt als weitere Integrationsklasse hinzu. Anschließend können die globalen Standards je Fähigkeit zwischen verschiedenen Anbietern umgeschaltet werden.

# Ziel der Folge

```text
bestehender Integrationsvertrag
        │
        ├── xAI / Grok
        └── OpenAI
                │
                ▼
Text · Bild · Speech-to-Text · Text-to-Speech

Video bleibt eine separat wählbare Fähigkeit.
```

Die Folge erklärt ausführlich, warum ein so kurzer Auftrag funktionieren kann:

- Der bestehende Code enthält bereits die Architektur und die Konventionen.
- Die Grok-Klasse ist ein vollständiges Beispiel für einen Anbieteradapter.
- Fähigkeiten, Einstellungen, Ergebnisse und Testwerkzeug sind anbieterneutral definiert.
- Tests und bestehende Oberflächen bilden einen ausführbaren Vertrag.
- Nur die anbieterspezifische Übersetzung zur OpenAI-API muss neu umgesetzt werden.

# Wichtiger aktueller Hinweis zur Videogenerierung

Die offizielle OpenAI-Dokumentation kennzeichnet die Sora-2-Modelle und die Videos API als veraltet. Die Abschaltung ist für den **24. September 2026** angekündigt.

Darum soll die neue OpenAI-Integration `video_generierung` nicht einfach blind von der Grok-Integration übernehmen. Empfohlen ist:

- OpenAI nicht als Standard für Videogenerierung zu verwenden,
- die Fähigkeit in der OpenAI-Integration standardmäßig nicht anzubieten,
- Grok oder einen anderen geeigneten Anbieter als Video-Standard beizubehalten,
- die aktuelle OpenAI-Dokumentation am Aufnahmetag erneut zu prüfen.

Gerade dieser Unterschied zeigt den Wert der Architektur: Nicht jede Integration muss jede Fähigkeit bereitstellen, und jede Fähigkeit darf einen anderen Standardanbieter verwenden.

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
# Offizielle OpenAI-Dokumentation

- Schnellstart und API-Key: https://developers.openai.com/api/docs/quickstart
- Modelle: https://developers.openai.com/api/docs/models
- Textgenerierung: https://developers.openai.com/api/docs/guides/text
- Bildgenerierung: https://developers.openai.com/api/docs/guides/image-generation
- Speech-to-Text: https://developers.openai.com/api/docs/guides/speech-to-text
- Text-to-Speech und Stimmen: https://developers.openai.com/api/docs/guides/text-to-speech
- Videogenerierung und Abschaltungshinweis: https://developers.openai.com/api/docs/guides/video-generation
- Deprecations: https://developers.openai.com/api/docs/deprecations

Modelle, Verfügbarkeit, Limits, Preise und Endpunkte können sich ändern. Vor der Aufnahme müssen die offiziellen Dokumente erneut geprüft werden.

# Ergebnis

Nach Folge 16 existieren Grok und OpenAI nebeneinander. Das vorhandene Testwerkzeug kann beide Integrationen verwenden, ohne für OpenAI neu gebaut zu werden. Die globalen Standards können pro Fähigkeit auf unterschiedliche Anbieter zeigen. Die Chat-Integration bleibt weiterhin einer späteren Folge vorbehalten.
