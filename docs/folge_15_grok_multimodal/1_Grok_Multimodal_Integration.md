# Noob2Claw – Folge 15: xAI/Grok als multimodale Integration

Version: 1.0

Dieses Dokument definiert die Erweiterung der Integrationsarchitektur um Text-, Bild-, Video- und Sprachdienste. xAI/Grok ist die erste Implementierung. Verbraucher im Noob2Claw-System bleiben anbieterneutral.

---

# 1. Leitentscheidung

```text
Fachliche Funktion
      │
      ▼
zentraler Fähigkeitsdienst
      │
      ▼
global gewählter Integrationseintrag
      │
      ├── xAI / Grok in Folge 15
      ├── OpenAI in Folge 16
      └── weitere geeignete APIs
```

Der zentrale Code fragt nach einer Fähigkeit und nicht nach einem Anbieter. Deshalb dürfen weder Testwerkzeug noch Agentenverwaltung direkt `XAIIntegration` instanziieren.

| Fähigkeit | Technischer Schlüssel | Aufgabe |
|---|---|---|
| Textgenerierung | `text_generierung` | Prompt in Text umwandeln |
| Bildgenerierung | `bild_generierung` | Prompt in Bilddatei umwandeln |
| Videogenerierung | `video_generierung` | Prompt in Videoauftrag beziehungsweise Videodatei umwandeln |
| Speech-to-Text | `speech_to_text` | Audio in Transkript umwandeln |
| Text-to-Speech | `text_to_speech` | Text mit einer Stimme in Audio umwandeln |

Eine Integration darf nur einen Teil der Fähigkeiten anbieten. Die globalen Standards können auf verschiedene Anbieter und Einträge zeigen.

---

# 2. Grok ist eine Option, keine Abhängigkeit

xAI stellt aktuell APIs für alle fünf Fähigkeiten bereit. Das macht xAI/Grok zu einer geeigneten ersten Referenzintegration.

Das bedeutet nicht, dass Grok zwingend ist, alle Anbieter dieselben Endpunkte besitzen oder eine OpenAI-kompatible Textschnittstelle automatisch Bild, Video und Audio kompatibel macht.

Wer xAI nicht nutzen will, setzt dennoch Datenmodell, Fähigkeitsdienst, Standardauswahl, Testwerkzeug, Stimmenzuordnung, Aufträge und Medienablage um. Der xAI-Eintrag bleibt deaktiviert beziehungsweise unkonfiguriert. Folge 16 ergänzt OpenAI über denselben Vertrag.

---

# 3. Erweiterter Integrationsvertrag

Der allgemeine Vertrag aus Folge 14 bleibt bestehen:

```php
informationen(): array
einstellungen(): array
datenabholung(int $eintrag_id, array $kontext = []): array
cronjob(int $eintrag_id, array $kontext = []): array
```

Generative Integrationen deklarieren zusätzliche Methoden:

```php
generiere_text(int $eintrag_id, array $anfrage): array
generiere_bild(int $eintrag_id, array $anfrage): array
generiere_video(int $eintrag_id, array $anfrage): array
video_status(int $eintrag_id, string $anbieter_auftrag_id): array
transkribiere_audio(int $eintrag_id, array $anfrage): array
generiere_sprache(int $eintrag_id, array $anfrage): array
stimmen(int $eintrag_id, bool $aktualisieren = false): array
modelle(int $eintrag_id, string $faehigkeit): array
```

`informationen()` meldet pro Fähigkeit mindestens Methode, Ein- und Ausgabemodalität sowie synchrone oder asynchrone Ausführung. Methodennamen aus Browseranfragen werden nie direkt ausgeführt.

---

# 4. Zentraler Fähigkeitsdienst

Der in Folge 14 eingeführte Aufruf `integration_standard_aufrufen()` bleibt als
kompatibler Einstiegspunkt bestehen. Er delegiert intern an den hier erweiterten
Fähigkeitsdienst. Es entsteht keine zweite parallele Aufrufarchitektur.

Empfohlene zentrale Funktionen:

```php
integration_faehigkeit_aufrufen(
    string $faehigkeit,
    array $anfrage,
    array $kontext = []
): array
```

```php
integration_faehigkeit_mit_eintrag_aufrufen(
    int $eintrag_id,
    string $faehigkeit,
    array $anfrage,
    array $kontext = []
): array
```

Die erste Funktion nutzt den globalen Standard. Die zweite erlaubt dem Integrationstest die bewusste Auswahl eines Eintrags.

Verbindliche Kompatibilität:

```php
integration_standard_aufrufen($faehigkeit, $methode, $optionen)
```

wird auf `integration_faehigkeit_aufrufen()` abgebildet. Die Registry prüft dabei,
dass `$methode` der für die deklarierte Fähigkeit erlaubten Methode entspricht.
Vorhandene Aufrufer aus Folge 14 bleiben funktionsfähig; neuer Code verwendet den
Fähigkeitsdienst. Ein direkter Aufruf der Anbieterklasse bleibt verboten.

Ablauf:

1. Fähigkeit gegen Allowlist prüfen.
2. Standard oder Eintrag laden.
3. Aktivstatus von Eintrag und Fähigkeit prüfen.
4. Klasse aus Registry laden.
5. deklarierte Methode und Vertrag prüfen.
6. Rechte, Limits und Kontext prüfen.
7. normalisierte Anfrage validieren.
8. Anbieteradapter aufrufen.
9. Antwort normalisieren und Lauf protokollieren.

---

# 5. Normalisiertes Ergebnis

Alle Fähigkeiten liefern dieselbe äußere Struktur:

```php
[
    'erfolg' => true,
    'status' => 'abgeschlossen',
    'faehigkeit' => 'text_to_speech',
    'anbieter' => 'xai',
    'integration_eintrag_id' => 7,
    'text' => '',
    'dateien' => [
        [
            'datei_id' => 314,
            'typ' => 'audio',
            'mime_type' => 'audio/mpeg',
            'dauer_sekunden' => 4.2,
        ],
    ],
    'anbieter_auftrag_id' => '',
    'modell' => '',
    'nutzung' => [],
    'kosten' => [],
    'fehlercode' => '',
    'meldung' => '',
    'wiederholbar' => false,
    'dauer_ms' => 0,
    'request_id' => '',
]
```

`dateien` enthält nur lokale, berechtigte Dateireferenzen. Temporäre Anbieter-URLs werden nicht als dauerhafte Systemreferenz ausgegeben.

Statuswerte mindestens: `wartend`, `laeuft`, `abgeschlossen`, `fehlgeschlagen`, `abgebrochen` und `abgelaufen`.

---

# 6. xAI-Integration

```text
Schlüssel: xai_grok
Titel: xAI / Grok
Basis-URL: https://api.x.ai/v1
Mehrere Einträge: ja
Fähigkeiten: alle fünf Fähigkeiten dieser Folge
```

Die Basis-URL ist fest in der Klasse hinterlegt. Der xAI-Adapter ist keine frei konfigurierbare Proxy-Integration.

## Konto und API-Key

Der Benutzer registriert sich unter `https://console.x.ai/`, richtet Team und Abrechnung beziehungsweise Guthaben ein und erzeugt einen normalen API-Key. Ein Management-API-Key ist für die Inference-Aufrufe nicht erforderlich.

Der Key sollte nur die benötigten Endpunkt- und Modellberechtigungen erhalten. Er wird ausschließlich serverseitig entgegengenommen, verschlüsselt, später nur maskiert und niemals in HTML, JavaScript, URL, Log oder Fehlermeldung ausgegeben.

## Einstellungen

| Gruppe | Felder |
|---|---|
| Allgemein | Bezeichnung, API-Key, Aktivstatus, Anbieter-Speicherung erlauben |
| Text | aktiv, Modell, Ausgabelimit, Timeout |
| Bild | aktiv, Modell, Anzahl, Seitenverhältnis, Auflösung/Qualität soweit unterstützt |
| Video | aktiv, Modell, Dauer, Seitenverhältnis, Auflösung, Timeout/Ablauf |
| Speech-to-Text | aktiv, Sprache/Automatik, maximale Dauer und Größe |
| Text-to-Speech | aktiv, Standardsprache, Standardstimme, Format, Geschwindigkeit, Zeichenlimit |
| Datenschutz | Aufbewahrung von Prompts, Transkripten, Rohantworten und Testmedien |

Optionen werden nur gezeigt, wenn API und Modell sie unterstützen.

---

# 7. Aktuelle xAI-Endpunkte

| Fähigkeit | Methode und Endpunkt | Verhalten |
|---|---|---|
| Text | `POST /v1/responses` | Textantwort |
| Bild | `POST /v1/images/generations` | Bildantwort beziehungsweise Medienreferenz |
| Video | `POST /v1/videos/generations` | liefert zunächst `request_id` |
| Videostatus | `GET /v1/videos/{request_id}` | Status und später Video |
| Speech-to-Text | `POST /v1/stt` | Multipart-Audio zu Transkript |
| Text-to-Speech | `POST /v1/tts` | Audiodaten beziehungsweise Audiohülle |
| Stimmen | `GET /v1/tts/voices` | verfügbare Stimmen |

Die Pfade werden relativ zur Basis-URL ohne doppeltes `/v1` zusammengesetzt. Authentifizierung erfolgt serverseitig über einen Bearer-Header.

Die Text-API kann mit OpenAI-Bibliotheken verwendet werden. Alle Modalitäten bleiben trotzdem hinter einem Anbieteradapter, da Parameter, Antworten und asynchrone Abläufe abweichen können.

---

# 8. Modellverwaltung

Modellnamen sind veränderlich. Deshalb:

- Modelle über dokumentierte Endpunkte laden, soweit möglich,
- anhand unterstützter Modalitäten filtern,
- Ergebnis mit Zeitstempel cachen,
- Aktion `Modelle aktualisieren`,
- gewähltes Modell bei temporärem Fehler nicht löschen,
- nicht mehr verfügbares Modell sichtbar markieren,
- keinen ungeprüften Modellnamen aus Requests verwenden,
- verwendetes Modell pro Testlauf dokumentieren.

Beispiele zum Planungszeitpunkt:

```text
Text:  grok-4.6
Bild:  grok-imagine-image-2.0
Video: grok-imagine-video-1.5
```

Vor Aufnahme entscheidet die aktuelle Modellliste des API-Keys.

---

# 9. Stimmen als Array

```php
[
    [
        'id' => 'eve',
        'name' => 'Eve',
        'sprache' => 'multilingual',
        'anbieter' => 'xai',
        'integration_eintrag_id' => 7,
        'vorschau_verfuegbar' => true,
        'metadaten' => [],
    ],
]
```

`id` dient dem API-Aufruf, `name` der Anzeige. Weitere Anbieter dürfen zusätzliche Metadaten liefern. Die Liste wird gecacht und über `Stimmen aktualisieren` erneuert. Entfernte Stimmen bleiben als ungültige alte Zuordnung nachvollziehbar.

---

# 10. Globale Integrationsstandards

Ergänze:

```text
Bildgenerierung
Textgenerierung
Videogenerierung
Speech-to-Text
Text-to-Speech
```

Je Auswahl erscheinen nur aktive Einträge mit passender aktiver Fähigkeit. Eine xAI-Konfiguration kann alle fünf Standards übernehmen; eine Mischung verschiedener Anbieter ist genauso zulässig.

Beim Deaktivieren eines verwendeten Eintrags werden betroffene Standards sichtbar genannt und bewusst entfernt oder die Aktion blockiert. Kein zufälliger Ersatz.

---

# 11. Testwerkzeug

Die xAI-Integration erhält einen registrierten Unterpunkt `Testen`. Die Seite nutzt zentrale Formular-, Button-, Datei- und Meldungskomponenten.

Gemeinsam sichtbar:

- konkreter Integrationseintrag,
- Verbindungsstatus und aktive Fähigkeiten,
- Hinweis auf mögliche Kosten,
- Links zu aktueller Preis- und Modelldokumentation,
- letzter Test mit Status und Dauer.

## Text

- Prompt, optionaler Systemtext und Ausgabelimit,
- Ergebnis als sicherer Text beziehungsweise Markdown,
- Modell, Laufzeit, Nutzung und Kopierfunktion.

## Bild

- Prompt und nur unterstützte Optionen,
- standardmäßig ein Bild,
- lokale Vorschau und sicherer Download,
- Modell, Laufzeit und Kostenmetadaten, wenn vorhanden.

## Video

- Prompt und unterstützte Videooptionen,
- Bestätigung vor kostenintensivem Start,
- lokaler Auftrag statt blockierendem Langzeitrequest,
- Status, Fortschritt, Player und Download,
- kontrollierte Fehler-, Ablauf- und Wiederholungsaktionen.

## Text → Sprache

- frei editierbarer Beispieltext,
- Stimme aus `stimmen()`, Sprache, Format und Geschwindigkeit,
- Button `Beispiel abspielen`,
- Erzeugung nur nach Klick und Schutz vor Mehrfachklick,
- Audio-Player und sicherer Download,
- optionaler Kurzzeit-Cache identischer Anfragen.

## Sprache → Text

- Audiodatei hochladen oder Mikrofonaufnahme,
- Sprache beziehungsweise automatische Erkennung,
- Transkript, erkannte Sprache und Dauer,
- Wortzeitpunkte nur wenn aktiviert und unterstützt,
- Kopierfunktion.

Testergebnisse werden nicht automatisch zu produktiven Inhalten.

---

# 12. Audioaufnahme

```text
Mikrofonfreigabe
      ↓
lokale Browseraufnahme
      ↓
Vorhören oder Verwerfen
      ↓
geschützter Upload
      ↓
zentraler Speech-to-Text-Aufruf
      ↓
Transkript im Testwerkzeug
```

Pflicht:

- bewusste Benutzeraktion und Feature-Erkennung,
- HTTPS und Mikrofonberechtigung,
- sichtbarer Timer und Aufnahmeindikator,
- Start, Pause falls unterstützt, Stop und Verwerfen,
- lokale Vorschau vor Upload,
- unterstützten MIME-Typ ermitteln,
- Grenzen für Dauer und Größe,
- MediaStream-Tracks zuverlässig schließen,
- serverseitige MIME- und Dateisignaturprüfung,
- zufällige sichere Dateinamen,
- temporäre Speicherung mit Bereinigung,
- keine direkte Browserkommunikation mit xAI,
- verständliche Zustände bei Ablehnung oder fehlender Unterstützung.

Die Aufnahme wird ausschließlich im Integrationstest eingesetzt.

---

# 13. Agentenstimmen und Abspieltest

Die Agentenverwaltung erhält eine anbieterneutrale Stimmenzuordnung. Stimmen stammen aus der globalen Text-to-Speech-Integration.

Auswahl:

- Option `Systemstandard`,
- Stimmenname, Sprache und Anbieter soweit verfügbar,
- Speicherung von Integrationseintrag und stabiler Stimmen-ID,
- Stimmenname als Snapshot.

Testbereich in der Agentenmaske:

- frei eingebbarer Beispieltext,
- editierbare Vorbelegung,
- Button `Beispiel abspielen`,
- TTS-Aufruf mit gerade gewählter Stimme,
- Audio-Player,
- Test speichert die Agentenzuordnung nicht automatisch,
- altes Test-Audio wird kontrolliert ersetzt oder bereinigt.

Beim Wechsel des globalen TTS-Standards wird eine unpassende alte Stimme markiert. Sie wird nie blind an den neuen Anbieter gesendet. Keine zufällige Stimme.

Die Stimme wird in Folge 15 nur konfiguriert und getestet, nicht im Chat verwendet.

---

# 14. Asynchrone Videogenerierung

```text
Testwerkzeug
    ↓ lokalen Auftrag anlegen
xAI-Video starten
    ↓ request_id speichern
Integrations-Dispatcher aus Folge 14
    ↓ Status zeitversetzt prüfen
Video sicher herunterladen und lokal speichern
```

Der Browser fragt nur lokalen Status ab. Er pollt nicht unkontrolliert xAI.

Ein Auftrag speichert mindestens:

```text
id
integration_eintrag_id
faehigkeit
anbieter_auftrag_id
status
fortschritt
anfrage_json_bereinigt
ergebnis_datei_id
fehlercode
fehlermeldung
erstellt_von
erstellt_am
gestartet_am
beendet_am
naechste_pruefung_am
ablauf_am
```

Der in Folge 14 erstmals geschaffene zentrale Cron-Einstieg und sein
Integrations-Dispatcher nutzen Backoff, Maximaldauer und atomare Sperren. Folge 15
erweitert diesen Mechanismus und erzeugt keinen zweiten Scheduler.

Videopolling wird als eigener erlaubter Aufgabentyp dieses Dispatchers
registriert. Jeder Medienauftrag wird atomar geclaimt. Pollintervall, begrenzter
exponentieller Backoff, maximale Gesamtdauer und terminale Statuswerte für Erfolg,
Fehler, Abbruch und Ablauf sind verbindlich. Nach einem terminalen Status wird der
Auftrag nicht erneut beim Anbieter abgefragt.

---

# 15. Medienablage

Bilder, Videos und TTS-Audio werden in der vorhandenen geschützten Dateiverwaltung gespeichert.

- Anbieter-URL nicht dauerhaft referenzieren,
- Download nur aus validierter Antwort,
- Host-Allowlist im Adapter und Weiterleitungen erneut prüfen,
- private Netzbereiche und unerwartete Protokolle blockieren,
- Timeout und maximale Größe,
- MIME-Typ und Dateisignatur prüfen,
- zufälliger Dateiname und sichere Endung,
- keine ausführbaren Formate,
- Zuordnung zu Benutzer, Eintrag und Testlauf,
- konfigurierbare Aufbewahrung und Bereinigung,
- geschützte Auslieferung mit Objektberechtigung.

---

# 16. Datenschutz und Kosten

Prompts, Audio und Transkripte können sensibel sein.

- Textanfragen ohne dauerhafte Anbieter-Speicherung senden, soweit unterstützt und nicht bewusst erlaubt.
- Testinhalte nicht ungefragt in allgemeine Logs übernehmen.
- Aufbewahrungsfrist und Löschfunktion für Testmedien vorsehen.
- Datenschutzhinweis vor Mikrofonaufnahme.
- API-Key und Authorization-Header immer redigieren.

Kosten- und Ressourcenschutz:

- Rate-Limit je Benutzer und Eintrag,
- maximale Prompt- und Ausgabelänge,
- kleine Standardzahl von Bildern,
- erlaubte Videodauern und Qualitäten,
- Bestätigung vor Videoauftrag,
- maximale TTS-Text- und Audioaufnahmelänge,
- Timeouts und begrenzte Wiederholungen,
- Nutzungs- und Kostenwerte speichern, wenn die Antwort sie liefert,
- aktuelle Preise verlinken statt dauerhaft versprechen.

Ein Timeout beweist nicht, dass beim Anbieter kein Auftrag entstand. Vor Wiederholung werden vorhandene Auftrags-ID und Idempotenzmöglichkeiten geprüft.

---

# 17. Datenmodell

Vorhandene Folge-14-Tabellen werden erweitert.

## Modell- und Stimmencache

```text
id
integration_eintrag_id
typ
externe_id
anzeigename
metadaten_json
aktiv
abgerufen_am
```

## Agentenstimme

```text
id
agent_id
integration_eintrag_id
stimme_id
stimme_name_snapshot
erstellt_am
geaendert_am
```

Eindeutig: `agent_id`.

## Medienaufträge und Testläufe

Nutze Abschnitt 14 oder eine vorhandene gleichwertige Jobtabelle. Integrationsläufe erhalten Fähigkeit, Modell, lokale Datei, bereinigte Nutzung und optionale Kostenmetadaten. Prompts und Transkripte werden nur gemäß Aufbewahrungseinstellung gespeichert.

Migrationen sind idempotent und nicht destruktiv.

---

# 18. Rechte und Fehler

Rechte sinngemäß:

```text
integrationen_ki_testen
integrationen_modelle_aktualisieren
integrationen_stimmen_aktualisieren
integrationen_medien_generieren
integrationen_audio_transkribieren
integrationen_medien_anzeigen
agenten_stimme_verwalten
```

Fehlercodes mindestens:

```text
nicht_konfiguriert
kein_standard
faehigkeit_deaktiviert
ungueltiger_api_key
modell_nicht_verfuegbar
stimme_nicht_verfuegbar
rate_limit
anbieter_timeout
anbieter_fehler
inhalt_abgelehnt
ungueltige_antwort
upload_ungueltig
datei_zu_gross
auftrag_abgelaufen
speicherung_fehlgeschlagen
```

Technische Details stehen im bereinigten internen Log. Benutzer sehen verständliche Meldung und nächsten sinnvollen Schritt.

---

# 19. Nicht Bestandteil dieser Folge

- Mikrofonbutton im Chat,
- automatische STT-Übernahme in Chatnachrichten,
- Vorlesen neuer Agentenantworten,
- TTS-Warteschlange im Chat,
- direkte Textgenerierung als Ersatz für den Agentenchat,
- OpenAI-Integrationsklasse; sie folgt in Folge 16.

Schnittstellen werden vorbereitet, diese Funktionen aber noch nicht implementiert.

---

# 20. Abnahmekriterien

1. Alle fünf Fähigkeiten sind anbieterneutral definiert.
2. xAI/Grok setzt die konfigurierten Fähigkeiten über dokumentierte Endpunkte um.
3. Ohne xAI-Key bleibt das System stabil und zeigt „nicht konfiguriert“.
4. Jede Fähigkeit besitzt eine eigene globale Standardauswahl.
5. Testwerkzeug testet Text, Bild, Video, TTS und STT getrennt.
6. Frei eingebbarer TTS-Beispieltext wird mit gewählter Stimme abgespielt.
7. Browseraudio kann aufgenommen, vorgehört, hochgeladen und transkribiert werden.
8. Modelle und Stimmen werden geladen, gecacht und aktualisiert.
9. Jeder Agent kann eine gültige Stimme oder `Systemstandard` erhalten.
10. Agentenmaske kann die gewählte Stimme mit eigenem Text testen.
11. Video läuft als asynchroner, wiederaufnehmbarer Auftrag.
12. Medien werden lokal, geschützt und kontrolliert gespeichert.
13. Secrets, Rechte, Datenschutz, Limits, Kostenhinweise und Fehlerfälle sind geprüft.
14. Keine spätere Chat-Integration wurde vorweggenommen.
15. OpenAI kann in Folge 16 als Adapter ergänzt werden, ohne Testwerkzeug, Standards oder Agentenstimmen neu zu bauen.

---

# 21. Offizielle Quellen

- https://console.x.ai/
- https://docs.x.ai/developers/quickstart
- https://docs.x.ai/developers/model-capabilities/text/generate-text
- https://docs.x.ai/developers/rest-api-reference/inference/images
- https://docs.x.ai/developers/rest-api-reference/inference/videos
- https://docs.x.ai/developers/model-capabilities/audio/speech-to-text
- https://docs.x.ai/developers/model-capabilities/audio/text-to-speech
- https://docs.x.ai/developers/rest-api-reference/inference/voice
- https://docs.x.ai/developers/models
- https://docs.x.ai/developers/cost-tracking

Modelle, Endpunkte, Preise und Limits sind vor Implementierung und Aufnahme erneut zu prüfen.
