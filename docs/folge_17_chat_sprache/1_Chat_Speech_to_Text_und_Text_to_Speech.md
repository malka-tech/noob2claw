# Folge 17 – Chat mit Speech-to-Text und Text-to-Speech

## 1. Ziel

Der vorhandene Noob2Claw-Chat verwendet die Sprachfähigkeiten aus dem Integrationssystem:

- Benutzer können eine Nachricht sprechen.
- Die konfigurierte Speech-to-Text-Integration erzeugt daraus einen Textentwurf.
- Der Benutzer kann den Text prüfen und bearbeiten.
- Der vorhandene Nachrichtenversand sendet anschließend eine normale Textnachricht.
- Neue Agentenantworten können auf Wunsch mit Text-to-Speech vorgelesen werden.
- Jeder Agent verwendet seine konfigurierte Stimme aus der aktuell aktiven TTS-Integration.

Die Erweiterung bleibt vollständig anbieterneutral.

---

## 2. Fachlicher Ablauf

### 2.1 Spracheingabe

```text
Benutzer klickt auf Mikrofon
        ↓
Browser fragt Mikrofonberechtigung an
        ↓
Aufnahme läuft mit Timer und sichtbarem Status
        ↓
Benutzer stoppt die Aufnahme
        ↓
lokale Vorschau: abspielen oder verwerfen
        ↓
Upload an Noob2Claw
        ↓
globaler Speech-to-Text-Standard
        ↓
normalisiertes Transkriptionsergebnis
        ↓
Text in den bestehenden Composer übernehmen
        ↓
Benutzer prüft, ändert und sendet
```

### 2.2 Vorlesen

```text
Benutzer aktiviert „Neue Agentenantworten vorlesen“
        ↓
neue Agentenantwort wird vollständig gespeichert
        ↓
Text wird in geeigneten Sprechtext umgewandelt
        ↓
globaler Text-to-Speech-Standard
        ↓
gültige Stimme des Agenten
        ↓
Audio wird erzeugt und geschützt gespeichert
        ↓
automatische Wiedergabe oder manueller Fallback
```

---

## 3. Zentrale Architekturentscheidung

Der Chat kennt nur Fähigkeiten:

```text
speech_to_text
text_to_speech
```

Er kennt keine konkreten Anbieterklassen.

```text
Chat
  ↓
zentraler Fähigkeitsdienst
  ↓
globaler Standardintegrationseintrag
  ↓
Registry
  ↓
Grok, OpenAI oder ein anderer kompatibler Adapter
```

Dadurch funktionieren dieselben Chatfunktionen mit jedem Anbieter, der den Vertrag erfüllt.

---

## 4. Aufnahmezustandsmaschine

Die Oberfläche soll nicht nur Buttons ein- und ausblenden, sondern einen eindeutigen Zustand verwalten.

```text
bereit
  └─ Aufnahme starten → berechtigung

berechtigung
  ├─ erlaubt → nimmt_auf
  └─ abgelehnt/Fehler → fehler

nimmt_auf
  ├─ pausieren → pausiert
  ├─ stoppen → aufgenommen
  ├─ Limit erreicht → aufgenommen
  └─ Fehler → fehler

pausiert
  ├─ fortsetzen → nimmt_auf
  ├─ stoppen → aufgenommen
  └─ verwerfen → bereit

aufgenommen
  ├─ abspielen → aufgenommen
  ├─ verwerfen → bereit
  └─ transkribieren → verarbeitet

verarbeitet
  ├─ Erfolg → bereit + Text im Composer
  └─ Fehler → aufgenommen oder fehler
```

Jeder Endzustand beendet aktive Mikrofonspuren zuverlässig.

---

## 5. Composer-Verhalten

Das Transkript wird als Entwurf behandelt.

Wenn das Eingabefeld leer ist:

- Transkript direkt einsetzen.

Wenn bereits Text vorhanden ist:

- nicht überschreiben,
- Auswahl `Anhängen`, `Ersetzen` oder `Abbrechen` anbieten.

Nach der Übernahme:

- Fokus in das Eingabefeld,
- Text bleibt editierbar,
- vorhandene Zeichenbegrenzung gilt,
- der normale Senden-Button bleibt der einzige Weg zur endgültigen Nachricht.

Damit durchläuft gesprochener Text dieselbe Validierung, Speicherung und Agentenverarbeitung wie getippter Text.

---

## 6. Temporäre Audiodatei

Die Rohaufnahme wird nur für Speech-to-Text benötigt.

Empfohlen:

- kurzlebige geschützte Datei,
- zufälliger interner Dateiname,
- Verknüpfung mit Benutzer, Chat und Ablaufzeit,
- serverseitige Typ- und Größenprüfung,
- Löschung nach erfolgreicher Transkription,
- bei Fehler kurze Frist für einen erneuten Versuch,
- anschließende Bereinigung durch den vorhandenen Aufräumjob.

Die Rohaufnahme wird nicht automatisch als Chat-Anhang gespeichert. Soll es später echte Sprachnachrichten geben, ist das eine eigene Funktion mit eigener Aufbewahrungs- und Anzeigeentscheidung.

---

## 7. Auswahl der Speech-to-Text-Integration

Der Server löst bei jeder Transkription den aktuellen globalen Standard für `speech_to_text` auf.

Prüfungen:

1. Standard vorhanden,
2. Integrationseintrag aktiv,
3. Klasse in Registry erlaubt,
4. Fähigkeit deklariert und für den Eintrag aktiv,
5. Benutzer darf die Funktion und den Chat verwenden,
6. Audiodatei gehört zu Benutzer und Chat,
7. Größen- und Zeitlimit eingehalten.

Ein früher geladener Anbietername aus dem Browser darf nicht als Vertrauensquelle dienen.

---

## 8. Vorleseeinstellung

Die Einstellung `Neue Agentenantworten vorlesen` ist:

- standardmäßig `false`,
- benutzerbezogen,
- auf den aktuellen Chat bezogen,
- serverseitig gespeichert,
- zusätzlich lokal sofort wirksam,
- für andere Benutzer ohne Auswirkung.

Falls das vorhandene Datenmodell keine eigenständigen Chats beziehungsweise Konversationen besitzt, wird die Einstellung pro Benutzer-Agent-Paar gespeichert. Die bestehende fachliche Identität entscheidet, nicht eine neue Parallel-ID.

---

## 9. Erkennung neuer Agentennachrichten

Automatisches Vorlesen darf nicht allein auf „Element wurde ins DOM eingefügt“ basieren. Der Chat kennt eine fachliche Nachrichten-ID und einen Abschlussstatus.

Eine Nachricht ist automatisch geeignet, wenn:

```text
rolle = agent
status = abgeschlossen
nachrichten_id > zuletzt_gesehene_id oder explizites Neu-Ereignis
vorlesen_aktiv = true
sichtbarer_text != leer
noch_nicht_fuer_diesen_benutzer_eingeplant = true
```

Bei Streaming wird erst nach dem finalen Ereignis erzeugt. Reconnect, erneutes Polling und DOM-Neuaufbau dürfen keinen zweiten Auftrag auslösen.

---

## 10. Sprechtext statt Roh-Markdown

Für Text-to-Speech wird ein eigener Sprechtext erzeugt.

Beispiel:

````markdown
## Ergebnis

Öffne [die Dokumentation](https://example.org/sehr/lange/url).

```php
echo "Hallo";
```
````

wird sinngemäß zu:

```text
Ergebnis. Öffne die Dokumentation. Codeblock ausgelassen.
```

Regeln:

- nur sichtbaren Agententext verwenden,
- HTML entfernen statt auszuführen,
- Markdown-Struktur in natürliche Pausen umsetzen,
- lange URLs nicht vorlesen,
- Code standardmäßig auslassen,
- Bildquellen und interne Metadaten auslassen,
- maximale Länge anwenden,
- Ergebnis deterministisch erzeugen und hashen.

---

## 11. Auswahl der Text-to-Speech-Integration und Stimme

### 11.1 Integration

Der globale Standard `text_to_speech` bestimmt den konkreten Integrationseintrag.

### 11.2 Stimme

Die Stimme wird in dieser Reihenfolge bestimmt:

```text
gültige Agentenstimme für genau diesen Integrationseintrag
        ↓ sonst
explizit konfigurierte Standardstimme des Integrationseintrags
        ↓ sonst
verständlicher Fehler
```

Eine Stimmen-ID ist nur zusammen mit dem Integrationseintrag eindeutig.

```text
(integration_eintrag_id, stimme_id)
```

Der sichtbare Name dient nur der Anzeige und als Snapshot, nicht als technische Identität.

---

## 12. Agentenmaske

Die vorhandene Agentenmaske erhält beziehungsweise behält den Abschnitt `Stimme`.

Anzeigen:

- Name der aktiven globalen TTS-Integration,
- Bezeichnung des konkreten Eintrags,
- Zeitpunkt der letzten Stimmenaktualisierung,
- Auswahl `Systemstandard`,
- normalisierte Stimmenliste,
- gespeicherte Stimme,
- Status `verfügbar`, `nicht mehr verfügbar` oder `gehört zu anderer Integration`,
- freier Beispieltext,
- Button `Beispiel abspielen`,
- Audio-Player mit Stop- und Wiederholfunktion.

Wenn die globale TTS-Integration gewechselt wurde, wird die alte Stimme nicht gelöscht. Sie wird als nicht passend angezeigt, bis der Benutzer bewusst eine neue Auswahl speichert.

---

## 13. TTS-Idempotenz und Cache

Automatisches Vorlesen kann durch Polling oder Reconnect mehrfach angestoßen werden. Ein eindeutiger Schlüssel verhindert doppelte Kosten:

```text
nachricht_id
+ integration_eintrag_id
+ stimme_id
+ sprechtext_hash
+ format
```

Statuswerte aus der vorhandenen Medienauftragslogik:

```text
wartend
laeuft
abgeschlossen
fehlgeschlagen
abgebrochen
abgelaufen
```

Wenn ein gültiges abgeschlossenes Ergebnis existiert, wird dessen geschützte Datei wiederverwendet.

---

## 14. Wiedergabewarteschlange

Mehrere neue Antworten dürfen nicht gleichzeitig sprechen.

Die Browserlogik verwaltet:

```text
aktuelle_wiedergabe
warteschlange[]
vorlesen_aktiv
chat_id
```

Regeln:

- höchstens ein Audio spielt,
- neue Datei wird hinten eingereiht,
- Ende startet den nächsten Eintrag,
- Fehler überspringt kontrolliert und zeigt Status,
- Stop beendet aktuelle Wiedergabe,
- Ausschalten leert automatische Warteschlange,
- Chatwechsel beendet und leert,
- manueller Klick darf einen ausgewählten Eintrag priorisieren,
- identische Nachrichten-ID wird nicht doppelt eingereiht.

---

## 15. Autoplay-Fallback

`HTMLMediaElement.play()` liefert ein Promise. Dieses muss ausgewertet werden.

```text
play() erfolgreich
  → Status „Wird vorgelesen“

play() mit NotAllowedError abgelehnt
  → Status „Audio bereit“
  → sichtbarer Button „Jetzt abspielen“

anderer Wiedergabefehler
  → verständlicher Fehler und erneuter Versuch
```

Ein aktivierter Vorleseschalter verbessert die Chance auf erlaubte Wiedergabe, garantiert sie aber nicht. Browser, Geräteeinstellungen oder ein Hintergrund-Tab können Autoplay weiterhin blockieren.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/Media/Guides/Autoplay
- https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play
[/Sources]

---

## 16. Browserkompatibilität der Aufnahme

Mikrofonzugriff benötigt einen sicheren Kontext und Benutzerfreigabe. Aufnahmecontainer und Codecs unterscheiden sich zwischen Browsern.

Darum:

```text
Feature-Erkennung
      ↓
erlaubte Formate der Reihe nach prüfen
      ↓
erstes unterstütztes Format verwenden
      ↓
tatsächlichen MIME-Typ mit dem Blob senden
      ↓
Server validiert erneut
```

Beispiele für zu prüfende Kandidaten können `audio/webm;codecs=opus`, `audio/webm`, `audio/mp4` oder `audio/ogg;codecs=opus` sein. Die konkrete Reihenfolge soll zum serverseitig unterstützten Formatumfang passen.

[Sources]
- https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia
- https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder
- https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder/isTypeSupported_static
[/Sources]

---

## 17. Sicherheit

### Browser

- kein Anbieter-Key im JavaScript,
- kein direkter Anbieteraufruf,
- Mikrofon nur nach Aktion,
- Tracks und Object-URLs zuverlässig aufräumen,
- Ausgaben als Text behandeln,
- keine Anbieter-Rohantwort in HTML einfügen.

### Server

- Session, CSRF und Rechte,
- Chat- und Nachrichtenobjektberechtigung,
- parametrisierte Datenbankabfragen,
- Inhaltstyp und Dateisignatur prüfen,
- Größe und Dauer begrenzen,
- Zufallsnamen und geschützte Ablage,
- Secrets zentral entschlüsseln,
- Anbieterziele nur aus Registry und Konfiguration,
- Rohinhalte nicht allgemein loggen,
- sichere Dateiauslieferung.

### Missbrauchsschutz

- Benutzer- und IP-bezogene Rate-Limits nach bestehendem Muster,
- Idempotenzschlüssel,
- maximale offene Aufträge,
- keine TTS-Erzeugung für alte Historie,
- keine unendliche Wiedergabewarteschlange,
- Aufräumfristen für temporäre Medien.

---

## 18. Empfohlenes logisches Datenmodell

### Chat-Vorleseeinstellung

```text
benutzer_id
chat_id
vorlesen_aktiv
lautstaerke optional
erstellt_am
geaendert_am
```

Eindeutigkeit:

```text
(benutzer_id, chat_id)
```

Folge 11 definiert `agenten_chats` mit stabiler `chat_id`. Ein Fallback auf
`agent_id` ist in dieser Folgenreihenfolge nicht zulässig, weil ein Benutzer
mehrere Chats mit demselben Agenten besitzen kann.

### Verknüpfte TTS-Ausgabe

Vorhandenen Medienauftrag erweitern beziehungsweise verwenden:

```text
nachricht_id
integration_eintrag_id
stimme_id
sprechtext_hash
format
status
datei_id
fehlercode
erstellt_von
erstellt_am
beendet_am
ablauf_am
```

Eindeutigkeit schützt vor Doppelgenerierung.

### Agentenstimme

Vorhandene Struktur aus Folge 15 verwenden:

```text
agent_id
integration_eintrag_id
stimme_id
stimme_name_snapshot
geaendert_am
```

---

## 19. API-Verträge

### Transkription anfordern

Anfrage sinngemäß:

```text
POST vorhandene Chat-API
modul=agenten_chat
aktion=chat_audio_transkribieren
chat_id
audio
request_id
csrf_token
```

Normalisierte Antwort:

```json
{
  "erfolg": true,
  "text": "Erkannter und noch bearbeitbarer Text",
  "integration": {
    "eintrag_id": 7,
    "name": "OpenAI Produktion"
  },
  "modell": "aktuelles Modell",
  "dauer_ms": 1240,
  "meldung": ""
}
```

### TTS anfordern

```text
POST vorhandene Chat-API
modul=agenten_chat
aktion=chat_tts_anfordern
chat_id
nachricht_id
csrf_token
```

Der Server gewinnt Nachrichtentext, Agent, Stimme und Integration selbst. Diese sicherheitsrelevanten Werte werden nicht ungeprüft aus dem Browser übernommen.

Antwort synchron:

```json
{
  "erfolg": true,
  "status": "abgeschlossen",
  "datei_id": 456,
  "wiederverwendet": false
}
```

Antwort asynchron:

```json
{
  "erfolg": true,
  "status": "wartend",
  "auftrag_id": 789
}
```

---

## 20. Fehlercodes

Stabile interne Fehlercodes, sinngemäß:

```text
STT_STANDARD_FEHLT
TTS_STANDARD_FEHLT
INTEGRATION_INAKTIV
FAEHIGKEIT_NICHT_VERFUEGBAR
MIKROFON_NICHT_ERLAUBT
AUDIO_FORMAT_UNGUELTIG
AUDIO_ZU_GROSS
AUDIO_ZU_LANG
TRANSKRIPTION_LEER
STIMME_UNGUELTIG
TTS_LIMIT_UEBERSCHRITTEN
ANBIETER_RATE_LIMIT
ANBIETER_TIMEOUT
WIEDERGABE_BLOCKIERT
CHAT_ZUGRIFF_VERWEIGERT
NACHRICHT_NICHT_GEFUNDEN
```

Der Browser darf daraus verständliche deutsche Meldungen erzeugen. Anbietertexte werden nicht ungefiltert angezeigt.

---

## 21. Abnahmetests

### Happy Path: Spracheingabe

```text
Mikrofon starten
→ sprechen
→ stoppen
→ vorhören
→ transkribieren
→ Text korrigieren
→ senden
→ normale Benutzer-Nachricht erscheint
```

### Happy Path: Vorlesen

```text
Vorlesen aktivieren
→ neue Agentenantwort empfangen
→ vollständigen Text erkennen
→ TTS einmal erzeugen
→ korrekte Agentenstimme verwenden
→ abspielen
```

### Pflichtfehler

- Mikrofon abgelehnt,
- keine STT-Integration,
- Transkription leer,
- TTS-Integration fehlt,
- Stimme ungültig,
- Autoplay blockiert,
- Anbieter-Timeout,
- doppeltes Nachrichtenereignis,
- unberechtigter Datei- und Nachrichtenzugriff.

### Regression

- normaler Textchat,
- Nachrichtenstreaming,
- Integrationsverwaltung,
- Testwerkzeug,
- Agentenverwaltung,
- Grok- und OpenAI-Adapter,
- Medien- und Aufräumjobs.

---

## 22. Definition of Done

Die Umsetzung ist abgeschlossen, wenn:

- Aufnahme und Mikrofonfreigabe klar bedienbar sind,
- Audio lokal vorgehört und verworfen werden kann,
- die Aufnahme serverseitig sicher validiert wird,
- STT über den globalen Fähigkeitsstandard läuft,
- das Transkript bearbeitbar in den vorhandenen Composer gelangt,
- erst der bestehende Senden-Vorgang eine Nachricht erzeugt,
- automatisches Vorlesen bewusst aktivierbar und benutzerbezogen gespeichert ist,
- nur neue vollständige Agentenantworten vorgelesen werden,
- Integration und Stimme serverseitig korrekt aufgelöst werden,
- gleiche TTS-Aufträge nicht doppelt berechnet werden,
- Autoplay-Blockaden einen manuellen Fallback besitzen,
- Agentenstimmen aus der aktiven TTS-Integration stammen,
- alle Dateien geschützt ausgeliefert und fristgerecht bereinigt werden,
- Tests für Rechte, Fehler, Browserzustände und Regression erfolgreich sind,
- kein Chatcode direkt von Grok, OpenAI oder einem anderen Anbieter abhängt.
