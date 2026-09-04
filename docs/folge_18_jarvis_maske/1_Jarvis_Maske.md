# Folge 18 – Jarvis-Maske

## 1. Ziel

Noob2Claw erhält eine zusätzliche Sprachoberfläche mit zwei Schritten:

1. Alle verfügbaren KI-Agenten werden als visuelle Übersicht angezeigt.
2. Nach der Auswahl öffnet sich eine futuristische Voice-Link-Maske für die Unterhaltung mit genau diesem Agenten.

Die Oberfläche verwendet den bestehenden Chat sowie die Sprachfunktionen aus Folge 17. Sie erzeugt keine neue Anbieter- oder Nachrichtenarchitektur.

---

## 2. Architektur

```text
Jarvis-Oberfläche
      │
      ├── bestehende Agentenliste und Berechtigungen
      ├── bestehender Chat und Nachrichtenverlauf
      ├── speech_to_text über globalen Standard
      ├── text_to_speech über globalen Standard
      ├── Stimme des ausgewählten Agenten
      └── geschützte Medien- und Dateiverwaltung
```

Die Jarvis-Maske ist eine alternative Darstellung desselben Chats.

```text
Normaler Chat ─┐
               ├─→ dieselben Chats und Nachrichten
Jarvis-Maske ──┘
```

Eine Nachricht darf deshalb nicht dupliziert werden, nur weil sie in beiden Oberflächen erscheint.

---

## 3. Agenten-Lobby

Die Lobby zeigt ausschließlich aktive Agenten, die der aktuelle Benutzer im normalen Chat verwenden darf.

### Agentenkarte

```text
Agentenbild
Agentenname
kurze Beschreibung
Sprachbereitschaft
letzte Nutzung optional
Gespräch starten
```

### Sprachbereitschaft

| Status | Bedeutung |
|---|---|
| Bereit | STT, TTS und gültige Stimme sind vorhanden |
| Nur Text | Chat funktioniert, mindestens eine Sprachrichtung fehlt |
| STT fehlt | Spracheingabe ist nicht konfiguriert |
| TTS fehlt | Antworten können nicht gesprochen werden |
| Stimme prüfen | TTS ist vorhanden, Agentenstimme passt aber nicht |

„Online“ darf nur angezeigt werden, wenn ein echter technischer Zustand dahintersteht. Konfiguration ist nicht dasselbe wie Live-Verfügbarkeit des externen Anbieters.

---

## 4. Gespräch öffnen

Nach Agentenauswahl:

1. Agentenberechtigung serverseitig erneut prüfen.
2. Bestehenden Chat beziehungsweise vorhandene Konversation ermitteln.
3. Verlauf über den vorhandenen Nachrichtenweg laden.
4. TTS-Konfiguration und Agentenstimme prüfen.
5. Voice-Link-Oberfläche in `READY` öffnen.

Ein Chat wird nicht unnötig allein durch das Öffnen der Maske angelegt, wenn die vorhandene Architektur ihn erst beim ersten Senden erzeugt.

---

## 5. Conversation State Machine

```text
LOBBY
  └─ Agent wählen → READY

READY
  ├─ Aufnahme starten → ARMING → LISTENING
  ├─ Text senden → SENDING
  ├─ Agent wechseln → LOBBY oder READY
  └─ beenden → ENDING

LISTENING
  ├─ loslassen/stoppen → TRANSCRIBING
  ├─ verwerfen → READY
  ├─ Limit/Fehler → ERROR
  └─ beenden → ENDING

TRANSCRIBING
  ├─ Text erkannt → REVIEW
  ├─ leer/Fehler → ERROR
  └─ beenden → ENDING

REVIEW
  ├─ senden → SENDING
  ├─ neu aufnehmen → LISTENING
  ├─ bearbeiten → REVIEW
  └─ verwerfen → READY

SENDING
  ├─ gespeichert → AGENT_THINKING
  └─ Fehler → ERROR

AGENT_THINKING
  ├─ Antwort vollständig → VOICE_GENERATING
  ├─ nur Text möglich → READY
  └─ Fehler → ERROR

VOICE_GENERATING
  ├─ Audio bereit → AGENT_SPEAKING
  ├─ TTS nicht möglich → READY
  └─ Fehler → ERROR

AGENT_SPEAKING
  ├─ Audio beendet → READY
  ├─ stoppen → READY
  ├─ Push-to-Talk → LISTENING
  └─ beenden → ENDING

ERROR
  ├─ wiederholen → vorheriger sinnvoller Zustand
  ├─ Text verwenden → REVIEW oder READY
  └─ beenden → ENDING

ENDING
  └─ Ressourcen aufgeräumt → LOBBY
```

Der vorherige Zustand für `ERROR` wird kontrolliert gespeichert; es gibt keine beliebige Rückkehr in einen nicht mehr gültigen Audiozustand.

---

## 6. Push-to-Talk

### Leertaste

```text
keydown Space
→ Aufnahme starten

keyup Space
→ Aufnahme stoppen
→ automatisch transkribieren
```

Schutzregeln:

- nicht in Formularfeldern auslösen,
- `event.repeat` ignorieren,
- nur bei sichtbarer aktiver Seite,
- nur in erlaubten Zuständen,
- bei verlorener Taste durch `blur` und `visibilitychange` sicher stoppen,
- kein Scrollen während tatsächlich aktiver Push-to-Talk-Aufnahme,
- keine globale Blockierung der Leertaste außerhalb dieses Falls.

### Mikrofonbutton

Standardmäßig als zugänglicher Umschalter:

```text
Klick 1 → Start
Klick 2 → Stoppen und transkribieren
```

Der Button ist ein echtes semantisches Bedienelement. Eine optionale Haltefunktion verwendet Pointer Events mit vollständiger Abbruchbehandlung.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key
- https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/repeat
- https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events
[/Sources]

---

## 7. Aufnahmefluss

Die sichere Aufnahme aus Folge 17 wird wiederverwendet:

```text
Mikrofonfreigabe
→ MediaRecorder
→ Audiopegel
→ Stopp
→ geschützter Upload
→ speech_to_text
→ Textentwurf
```

In der Voice-Link-Ansicht entfällt eine separate Vorschauseite. Der Benutzer kann den erkannten Text prüfen und bei Bedarf `Neu aufnehmen` wählen. Falls Folge 17 bereits eine lokale Audiovorschau als Komponente anbietet, darf sie in einem kleinen erweiterten Bereich weiterhin erreichbar sein.

Die Aufnahme wird niemals beim Öffnen der Seite automatisch gestartet.

---

## 8. Transkription und Review

Die Oberfläche zeigt während `TRANSCRIBING`:

- `Ich verstehe dich`,
- verwendete STT-Integration als unaufdringliche echte Information,
- verstrichene Verarbeitungszeit,
- Abbruch nur, wenn der Serverauftrag dies wirklich unterstützt.

Nach Erfolg:

- Text prominent anzeigen,
- Editor beziehungsweise Composer fokussierbar machen,
- `Senden`, `Neu aufnehmen` und `Verwerfen` anbieten,
- leeren Text nicht senden,
- Länge und vorhandene Chatregeln anwenden.

Es gibt keine behauptete Live-Transkription, wenn der Providervertrag nur abgeschlossene Dateien verarbeitet.

---

## 9. Nachrichtenversand

`Senden` nutzt denselben serverseitigen Nachrichtenweg wie der normale Chat.

Dadurch bleiben erhalten:

- Rollen und Agentenzuordnung,
- Chat- und Nachrichtenberechtigungen,
- Validierung,
- Speicherung,
- Streaming oder Polling,
- Fehlerbehandlung,
- Audit-Metadaten,
- spätere Anzeige im normalen Chat.

Die Jarvis-Maske erzeugt keinen eigenen Nachrichtentyp `voice`, weil der bestätigte Inhalt Text ist.

---

## 10. Agentenantwort

Während der Antwort:

- `AGENT_THINKING` anzeigen,
- neue Textteile im Transkript rendern,
- Agentenkern ruhig reaktiv animieren,
- keine TTS-Erzeugung pro Token,
- endgültige Nachrichten-ID und Abschlussstatus abwarten.

Nach Abschluss:

- Sprechtext aus sichtbarem Inhalt erzeugen,
- TTS-Auftrag idempotent anfordern,
- Agentenstimme auflösen,
- Audio geschützt laden,
- `AGENT_SPEAKING` anzeigen,
- Audio und Text synchron als dieselbe Nachricht kennzeichnen.

---

## 11. Agentenstimme

Die bestehende Zuordnung aus Folge 15 und 17 ist verbindlich.

```text
agent_id
integration_eintrag_id
stimme_id
```

Gültigkeitsprüfung:

```text
aktueller globaler TTS-Eintrag
      = gespeicherter Stimmen-Eintrag
und Stimme in normalisierter Liste vorhanden
```

Ist die Stimme ungültig:

- Antwort weiterhin als Text anzeigen,
- ausdrücklich konfigurierte TTS-Standardstimme nur nach bestehender Regel verwenden,
- sonst Sprache deaktivieren,
- Verknüpfung zur Agentenmaske für Korrektur anbieten,
- keine zufällige Stimme auswählen.

---

## 12. Agentensprache unterbrechen

Ein neues Push-to-Talk-Ereignis während `AGENT_SPEAKING`:

```text
lokales Audio stoppen
→ Wiedergabeposition zurücksetzen
→ Warteschlange behandeln
→ Aufnahme starten
```

Die bereits gespeicherte Textantwort bleibt im Transkript. Ein späterer manueller Abspielbutton darf sie erneut vorlesen.

Die Oberfläche unterscheidet:

- `Audio gestoppt`,
- `Agentenprozess abgebrochen`, falls wirklich unterstützt.

Diese Zustände dürfen nicht verwechselt werden.

---

## 13. Gespräch beenden

Vor dem Beenden prüfen:

- läuft eine Aufnahme,
- existiert ein ungesendeter Text,
- läuft Agenten-Audio,
- ist ein Nachrichtenrequest aktiv.

Bei flüchtigen Daten erscheint eine Bestätigung. Danach:

- Aufnahme stoppen,
- Mikrofontracks schließen,
- Object-URLs freigeben,
- Audio stoppen,
- AudioContext schließen,
- Event Listener entfernen,
- Animationsframes abbrechen,
- flüchtige Warteschlange leeren,
- zur Lobby zurückkehren.

Persistierte Chats und Nachrichten bleiben erhalten.

---

## 14. Zustand und sichtbare Wahrheit

Jede Anzeige muss auf echten Daten beruhen.

| Anzeige | Datenquelle |
|---|---|
| Gesprächsdauer | lokale Session-Startzeit |
| Nachrichtenanzahl | geladene beziehungsweise serverseitig gemeldete Nachrichten |
| STT-Latenz | gemessener eigener Request |
| TTS-Latenz | gemessener eigener Auftrag |
| Verbunden | tatsächlicher lokaler Chat-/API-Zustand |
| Agentenstimme | serverseitig aufgelöste Zuordnung |
| Integration | aktuell global aufgelöster Eintrag |

Keine zufälligen Prozentwerte, Pingzeiten, Sicherheitsstufen oder Systemcodes als bloße Dekoration.

---

## 15. Frontend-Zustand

Ein zentraler Store beziehungsweise Controller verwaltet sinngemäß:

```js
{
  view: 'lobby',
  phase: 'READY',
  agentId: null,
  chatId: null,
  transcriptDraft: '',
  recorder: null,
  mediaStream: null,
  audioContext: null,
  currentAudio: null,
  requestIds: {},
  speakerEnabled: true,
  effectMode: 'standard',
  lastError: null
}
```

Keine geheimen Werte, Anbieter-Keys oder ungefilterten Rohantworten im Zustand speichern.

State-Transitions laufen über definierte Aktionen, nicht über beliebige direkte Feldänderungen aus vielen Event-Handlern.

---

## 16. Ereignisse

Wichtige UI-Ereignisse:

```text
AGENT_SELECTED
SESSION_READY
PTT_STARTED
PTT_RELEASED
RECORDING_STOPPED
TRANSCRIPTION_SUCCEEDED
TRANSCRIPTION_FAILED
DRAFT_CHANGED
MESSAGE_SENT
AGENT_STREAM_STARTED
AGENT_MESSAGE_COMPLETED
TTS_READY
TTS_FAILED
PLAYBACK_STARTED
PLAYBACK_ENDED
PLAYBACK_STOPPED
SESSION_END_REQUESTED
SESSION_ENDED
PAGE_HIDDEN
```

Jedes asynchrone Ergebnis trägt eine Request-, Chat- und Agentenidentität. Verspätete Antworten einer vorherigen Session dürfen den aktuellen Zustand nicht überschreiben.

---

## 17. Agentenwechsel

Sicherer Wechsel:

1. Zielagent gewählt.
2. Berechtigung prüfen.
3. laufende Aufnahme stoppen oder Benutzerentscheidung anfordern.
4. ungesendeten Entwurf behandeln.
5. Audio stoppen.
6. vorherige asynchrone UI-Ergebnisse durch Session-ID entwerten.
7. Zielchat laden.
8. TTS-Stimme und Sprachbereitschaft neu auflösen.
9. Zustand auf `READY` setzen.

Ein verspätetes TTS- oder STT-Ergebnis des vorherigen Agenten darf nicht in der neuen Session erscheinen.

---

## 18. API-Wiederverwendung

Vorzugsweise unverändert verwenden:

```text
Agentenliste
Chatverlauf laden
chat_audio_transkribieren
Chatnachricht senden
Agentenantwort abrufen oder streamen
chat_tts_anfordern
chat_tts_status
geschützte Audioauslieferung
```

Nur UI-spezifische Einstellungen wie letzter Agent oder Effektmodus dürfen einen kleinen neuen Endpunkt benötigen, wenn das allgemeine Einstellungssystem sie nicht bereits speichern kann.

---

## 19. Idempotenz

### Speech-to-Text

Eine abgeschlossene Aufnahme besitzt eine eindeutige lokale und serverseitige Request-ID. Mehrfacher Klick auf `Senden` oder erneutes Rendern löst keine zweite Transkription derselben Datei aus.

### Chatnachricht

Der vorhandene Nachrichten-Idempotenzmechanismus verhindert Doppelversand.

### Text-to-Speech

Vorhandener Schlüssel aus Folge 17:

```text
nachricht_id
+ integration_eintrag_id
+ stimme_id
+ sprechtext_hash
+ format
```

### UI

Asynchrone Antwort wird nur übernommen, wenn `session_id`, `agent_id`, `chat_id` und aktuelle Request-ID noch passen.

---

## 20. Fehlerbehandlung

### Aufnahmefehler

- Mikrofonzugriff erklären,
- Texteingabe anbieten,
- neue Aufnahme erlauben.

### STT-Fehler

- Aufnahme optional innerhalb kurzer Frist erneut transkribieren,
- neu aufnehmen,
- Text schreiben,
- keine erfundene Transkription erzeugen.

### Chatfehler

- Entwurf erhalten,
- erneutes Senden kontrolliert ermöglichen,
- doppelte Nachricht verhindern.

### TTS-Fehler

- Textantwort bleibt sichtbar,
- manuell erneut versuchen,
- Agentenstimme beziehungsweise Integration prüfen,
- Antwort nicht erneut vom Agenten generieren.

### Verbindungsfehler

- flüchtigen Entwurf erhalten,
- Animationen beruhigen,
- neue Aktionen bis zur Klärung begrenzen,
- erneutes Verbinden anbieten.

---

## 21. Sicherheit

- Route und APIs prüfen `jarvis_nutzen`.
- Agentenliste wird serverseitig gefiltert.
- Chat- und Nachrichten-IDs werden objektbezogen autorisiert.
- STT- und TTS-Integration wird serverseitig ausgewählt.
- Agentenstimme wird serverseitig aufgelöst.
- API-Keys verlassen nie den Server.
- Audio wird nur temporär oder geschützt gespeichert.
- Nachrichten werden sicher gerendert.
- keine unkontrollierten HTML-, SVG- oder Skriptinhalte aus Agentenantworten.
- Content Security Policy wird nicht für Effekte aufgeweicht.
- lokale Assets und vorhandene Agentenbilder verwenden.
- Rate-Limits und maximale offene Requests einhalten.

---

## 22. Datenschutz

Die Oberfläche zeigt vor der ersten Aufnahme einen knappen verständlichen Hinweis:

```text
Deine Aufnahme wird zur Spracherkennung über die konfigurierte Integration verarbeitet.
```

Zusätzlich:

- Namen des aktuellen STT- und TTS-Eintrags in technischen Details anzeigen,
- Rohaufnahme standardmäßig kurz halten,
- keine dauerhafte Hintergrundaufnahme,
- Gesprächsende stoppt Mikrofon sofort,
- Transkript bleibt Bestandteil des normalen Chatverlaufs,
- bestehende Aufbewahrungs- und Löschregeln des Chats anwenden.

---

## 23. Performance und Sichtbarkeit

- Animationen verwenden `requestAnimationFrame()` oder CSS.
- Fachzustand hängt nie von einer Animationsschleife ab.
- `document.hidden` pausiert visuelle Schleifen.
- laufende Aufnahme wird beim Verbergen sicher beendet.
- Audio wird nicht überraschend beim Zurückkehren fortgesetzt.
- Partikel und Canvas-Auflösung sind begrenzt.
- Transkriptvirtualisierung oder Pagination nach vorhandenem Muster bei großen Verläufen.
- keine neue schwere 3D-Abhängigkeit nur für Dekoration.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API
- https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame
[/Sources]

---

## 24. Barrierefreiheit

- semantische Landmarken für Agenten, Hauptbühne und Transkript,
- Agentenkarten vollständig per Tastatur bedienbar,
- Fokus bei Ansichtswechsel gezielt setzen,
- sichtbarer Fokus auf dunklem Hintergrund,
- Zustandsmeldung über `aria-live`,
- keine schnelle Daueransage des Audiopegels,
- Transkript bleibt als Text erhalten,
- Mikrofon, Senden, Stopp und Beenden mit eindeutigen Namen,
- 200-Prozent-Zoom prüfen,
- `prefers-reduced-motion` respektieren,
- keine kritischen Lichtblitze,
- Touchziele ausreichend groß,
- Fallback auf normale Texteingabe.

---

## 25. Abnahmekriterien

Die Umsetzung ist fertig, wenn:

- Navigation und Recht funktionieren,
- alle erlaubten aktiven Agenten auswählbar sind,
- ein Agent sicher an den vorhandenen Chat gebunden wird,
- Leertaste und Mikrofonbutton zuverlässig aufnehmen,
- Fokusverlust keine Daueraufnahme verursacht,
- STT über die globale Integration läuft,
- erkannter Text vor dem Senden bearbeitbar bleibt,
- normaler Chatversand genau eine Nachricht erzeugt,
- fertige Agentenantwort im Transkript erscheint,
- TTS die gültige Agentenstimme verwendet,
- Sprache gestoppt und durch neue Aufnahme unterbrochen werden kann,
- Gesprächsende alle flüchtigen Ressourcen aufräumt,
- Designzustände fachlich korrekt animiert sind,
- Sparmodus und reduzierte Bewegung funktionieren,
- responsive und barrierefreie Bedienung geprüft ist,
- normaler Chat und Integrationen keine Regression zeigen,
- keine direkte Abhängigkeit zu Grok, OpenAI oder einem anderen Anbieter entstanden ist.
