# Noob2Claw – Folge 17: Sprechen und Vorlesen im Chat

In Folge 17 verwendet der vorhandene Chat erstmals die anbieterneutralen Sprachfähigkeiten aus den Integrationen.

Benutzer können eine Nachricht mit dem Mikrofon aufnehmen. Die global eingestellte Speech-to-Text-Integration wandelt die Aufnahme in Text um. Der erkannte Text landet im vorhandenen Eingabefeld, kann korrigiert werden und wird anschließend wie jede andere Textnachricht gesendet.

Neue Antworten eines Agenten können auf Wunsch automatisch vorgelesen werden. Dafür verwendet der Chat die global eingestellte Text-to-Speech-Integration und die Stimme, die dem jeweiligen Agenten zugeordnet wurde.

# Zielbild

```text
Benutzer spricht
      ↓
Speech-to-Text-Standardintegration
      ↓
prüfbarer und bearbeitbarer Text
      ↓
bestehender Nachrichtenversand

Agent antwortet
      ↓
Text-to-Speech-Standardintegration
      ↓
Stimme des Agenten
      ↓
automatisch oder manuell abspielen
```

# Wichtige Grundsätze

- Der Chat ruft niemals Grok, OpenAI oder eine andere Anbieterklasse direkt auf.
- Speech-to-Text und Text-to-Speech werden ausschließlich über den zentralen Fähigkeitsdienst verwendet.
- Die Mikrofonaufnahme wird nicht ungeprüft als Nachricht versendet.
- Der erkannte Text bleibt vor dem Senden bearbeitbar.
- Automatisches Vorlesen ist standardmäßig ausgeschaltet und wird bewusst im Chat aktiviert.
- Nur neue, vollständig empfangene Agentenantworten werden automatisch vorgelesen.
- Falls der Browser Autoplay blockiert, erscheint ein klarer manueller Abspielknopf.
- Stimmen stammen aus der aktuell global eingestellten Text-to-Speech-Integration.
- Eine alte oder unpassende Stimmen-ID wird niemals stillschweigend einem anderen Anbieter zugeordnet.
- Audio, Transkripte, Kosten und Anbieterfehler werden sicher und datensparsam behandelt.

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
- `1_Chat_Speech_to_Text_und_Text_to_Speech.md` – technische und fachliche Spezifikation

# Abgrenzung

Diese Folge erweitert den vorhandenen Chat. Sie baut kein zweites Chatsystem und keine neue Sprachintegration. Die Anbieterintegrationen, globalen Standards, Stimmenlisten, Medienablage und Agentenzuordnung aus Folge 15 und 16 werden wiederverwendet.

Die Audioaufnahme dient in dieser Folge der Transkription. Eine dauerhafte Sprachnachricht als eigener Chat-Anhang ist nicht Bestandteil der Aufgabe.

# Browsergrundlagen

- Mikrofonzugriff mit `getUserMedia()`: https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia
- Aufnahme mit `MediaRecorder`: https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder
- Ermittlung unterstützter Aufnahmeformate: https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder/isTypeSupported_static
- Regeln für automatische Audiowiedergabe: https://developer.mozilla.org/en-US/docs/Web/Media/Guides/Autoplay
- Fehlerbehandlung von `HTMLMediaElement.play()`: https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play

# Ergebnis

Nach Folge 17 kann ein Benutzer im Chat sprechen statt tippen und neue Agentenantworten mit der konfigurierten Agentenstimme anhören. Alle Funktionen bleiben anbieterneutral und funktionieren mit jeder Integration, die den bestehenden Speech-to-Text- beziehungsweise Text-to-Speech-Vertrag erfüllt.
