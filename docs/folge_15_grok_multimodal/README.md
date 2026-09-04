# Noob2Claw – Folge 15: Grok für Text, Bilder, Videos und Sprache

In Folge 15 wird das Integrationssystem aus Folge 14 um generative KI-Fähigkeiten erweitert. Als erster Anbieter wird die API von xAI mit Grok und Grok Imagine angebunden.

Die Integration unterstützt:

- Textgenerierung,
- Bildgenerierung,
- Videogenerierung,
- Speech-to-Text,
- Text-to-Speech,
- dynamisch geladene Stimmen.

# 🌐 Noob2Claw-Webseite

Weitere Informationen, Anleitungen und Neuigkeiten zum Projekt findet ihr auf [noob2claw.de](https://noob2claw.de).

---

# Grok ist optional

Die Architektur ist nicht an Grok gebunden. Statt xAI kann später OpenAI oder jeder andere Anbieter verwendet werden, der die benötigten Fähigkeiten über eine geeignete API bereitstellt.

In Folge 16 wird dieselbe Integrationsarchitektur mit OpenAI umgesetzt. Wer xAI beziehungsweise Grok nicht verwenden möchte, sollte Folge 15 trotzdem vollständig umsetzen. Übersprungen werden können nur:

- Registrierung bei xAI,
- Hinterlegung eines xAI-API-Keys,
- kostenpflichtige Live-Aufrufe der xAI-API.

Klassenvertrag, globale Fähigkeiten, Testwerkzeug und Stimmenzuordnung werden in Folge 16 vorausgesetzt.

---

# Ziel der Folge

```text
Integrationsverwaltung und Testwerkzeug
        │
        ▼
anbieterneutrale KI-Fähigkeiten
        │
        ├── text_generierung
        ├── bild_generierung
        ├── video_generierung
        ├── speech_to_text
        └── text_to_speech
                    │
                    ▼
              xAI / Grok
```

Noob2Claw soll nicht direkt mit xAI-Endpunkten verdrahtet werden. Das System ruft eine Fähigkeit auf; die gewählte Standardintegration übernimmt die anbieterspezifische Umsetzung.

---

# Was wird umgesetzt?

- xAI-Konto und API-Key über [console.x.ai](https://console.x.ai/)
- sichere Speicherung und Prüfung des API-Keys
- Erweiterung des Integrationsvertrags um generative Fähigkeiten
- normalisierte Ein- und Ausgabeformate für Text und Medien
- Testwerkzeug für Text, Bild, Video, Text-to-Speech und Speech-to-Text
- Audioaufnahme direkt im Testwerkzeug
- neue globale Standards für alle fünf Fähigkeiten
- dynamische Stimmenliste der Text-to-Speech-Integration
- Zuordnung einer Stimme zu jedem Agenten
- Testbutton, der einen frei eingegebenen Beispieltext mit der gewählten Stimme abspielt
- Audioaufnahme im Testwerkzeug mit anschließender Transkription
- sichere Speicherung und Auslieferung erzeugter Mediendateien
- Statusverwaltung für asynchrone Videogenerierung
- Kosten-, Größen-, Laufzeit- und Berechtigungsgrenzen
- anbieterneutrale Vorbereitung der OpenAI-Integration in Folge 16

---

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
- `1_Grok_Multimodal_Integration.md` – Architektur, Fähigkeiten, Testwerkzeug und Stimmenzuordnung

# Offizielle Dokumentation

- xAI Console: https://console.x.ai/
- API Quickstart: https://docs.x.ai/developers/quickstart
- Textgenerierung: https://docs.x.ai/developers/model-capabilities/text/generate-text
- Bildgenerierung: https://docs.x.ai/developers/rest-api-reference/inference/images
- Videogenerierung: https://docs.x.ai/developers/rest-api-reference/inference/videos
- Speech-to-Text: https://docs.x.ai/developers/model-capabilities/audio/speech-to-text
- Text-to-Speech: https://docs.x.ai/developers/model-capabilities/audio/text-to-speech
- Voice API: https://docs.x.ai/developers/rest-api-reference/inference/voice
- Modelle: https://docs.x.ai/developers/models
- Kostenübersicht: https://docs.x.ai/developers/cost-tracking

Modellnamen, Preise, Limits und Verfügbarkeit können sich ändern und müssen vor der Aufnahme erneut in der offiziellen Dokumentation geprüft werden.

---

# Ergebnis

Nach dieser Folge besitzt Noob2Claw eine anbieterneutrale Grundlage für generative KI. xAI/Grok ist die erste konkrete Implementierung. Benutzer können alle Fähigkeiten in der Integrationsverwaltung testen und jedem Agenten eine Stimme zuordnen. Die Einbindung dieser Funktionen in den Chat folgt erst in einer späteren Folge.
