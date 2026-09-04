# Noob2Claw – Folge 15: Startprompt
# Grok für Text, Bilder, Videos und Sprache anbinden

Du arbeitest am bestehenden Projekt Noob2Claw.

In Folge 14 wurde eine allgemeine Integrationsverwaltung mit mehreren Einträgen, Fähigkeiten, globalen Standards und zentraler Cronjob-Ausführung geschaffen. Folge 15 erweitert diese Grundlage um generative KI-Fähigkeiten. Als erste konkrete Implementierung wird xAI mit Grok und Grok Imagine angebunden.

Das Ziel ist nicht, Noob2Claw fest mit Grok zu verdrahten.

```text
anbieterneutrale Fähigkeiten
    +
xAI/Grok als erste Implementierung
    +
Testwerkzeug für alle Modalitäten
    +
globale Standardintegration je Fähigkeit
    +
Stimmenzuordnung je Agent
```

Die Integration in den bestehenden Chat ist ausdrücklich nicht Bestandteil dieser Folge.

---

# 1. Wichtiger Hinweis für Benutzer ohne Grok

Grok ist optional. Das Integrationssystem muss ebenso mit OpenAI oder anderen geeigneten APIs funktionieren können.

In Folge 16 wird auf derselben Architektur eine OpenAI-Integration umgesetzt. Wer xAI nicht verwenden möchte, soll Folge 15 trotzdem ausführen und nur diese Schritte auslassen:

- Registrierung bei xAI,
- Erzeugung und Hinterlegung eines xAI-API-Keys,
- kostenpflichtige Live-Tests gegen xAI.

Alle anbieterneutralen Änderungen sind trotzdem erforderlich:

- neue Fähigkeiten und Verträge,
- globale Standardauswahl,
- normalisierte Ein- und Ausgabeformate,
- Testwerkzeug,
- Audioaufnahme im Testwerkzeug,
- asynchrone Medienaufträge,
- Stimmenmodell und Agenten-Zuordnung,
- Rechte, Migrationen, Speicherung und Fehlerbehandlung.

Ohne xAI-Key muss die Oberfläche einen sauberen Zustand „nicht konfiguriert“ zeigen. Sie darf nicht abstürzen und keine xAI-Verbindung vortäuschen.

---

# 2. Repository und Grundlagen

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

Aktualisiere die Vorlage vorsichtig. Bewahre vorhandene lokale Arbeit. Lies anschließend vollständig:

- sämtliche Dokumente unter `docs/folge_9_framework/`,
- Folge 10 für Agentenverwaltung und Agentendaten,
- Folge 14 für Integrationsklassen, Einträge, Standards, Cronjobs und Logs,
- `docs/folge_15_grok_multimodal/1_Grok_Multimodal_Integration.md` als primäre Aufgabenbeschreibung.

Die vorhandenen Architektur-, Datenbank-, Rechte-, Formular-, Tabellen-, Upload-, Sicherheits- und Logging-Standards sind verbindlich.

---

# 3. Aktiven Projektstand analysieren

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

- Integrationsvertrag und Registry aus Folge 14,
- Integrationseinträgen und Konfigurationswerten,
- globalen Integrationsstandards,
- zentralem HTTP-Client,
- Secret-Verschlüsselung,
- Upload- und Dateiverwaltung,
- Hintergrundaufträgen und Integrations-Cronjobs,
- Agentenverwaltung und Agenten-Einstellungsmaske,
- Rechte-, CSRF- und Logging-Funktionen,
- vorhandenen Audio-, Video- oder Media-Komponenten.

Erweitere vorhandene Funktionen. Baue keine parallele Integrations-, Datei-, Job- oder Rechtearchitektur.

---

# 4. Offizielle xAI-Dokumentation prüfen

Prüfe vor der Implementierung die jeweils aktuelle Dokumentation:

```text
https://console.x.ai/
https://docs.x.ai/developers/quickstart
https://docs.x.ai/developers/model-capabilities/text/generate-text
https://docs.x.ai/developers/rest-api-reference/inference/images
https://docs.x.ai/developers/rest-api-reference/inference/videos
https://docs.x.ai/developers/model-capabilities/audio/speech-to-text
https://docs.x.ai/developers/model-capabilities/audio/text-to-speech
https://docs.x.ai/developers/rest-api-reference/inference/voice
https://docs.x.ai/developers/models
https://docs.x.ai/developers/cost-tracking
```

Modellnamen, Preise, Regionen, Limits und Antwortformate können sich ändern. Verwende dokumentierte aktuelle Endpunkte. Modellbezeichner gehören in die Integrationseinstellungen oder werden über die API geladen; sie dürfen nicht an vielen Stellen im Code verteilt sein.

---

# 5. Pflichtziele

1. Vorhandenen Integrationsvertrag um fünf anbieterneutrale Fähigkeiten erweitern.
2. xAI/Grok als konkrete Integrationsklasse umsetzen.
3. xAI-API-Key sicher speichern und testen.
4. Modelle und Stimmen je Fähigkeit verwalten beziehungsweise laden.
5. Testwerkzeug für Text, Bild, Video, Text-to-Speech und Speech-to-Text erstellen.
6. Im Text-to-Speech-Test einen frei eingebbaren Beispieltext abspielen können.
7. Im Speech-to-Text-Test Ton im Browser aufnehmen und transkribieren können.
8. Globale Standardintegration für jede der fünf Fähigkeiten auswählbar machen.
9. Stimmen als normalisiertes Array durch die Integration liefern.
10. Jedem Agenten eine Stimme zuordnen können.
11. Asynchrone Videogenerierung robust verarbeiten.
12. Erzeugte Medien sicher lokal speichern und ausliefern.
13. Kosten-, Größen-, Laufzeit- und Rechtebegrenzungen ergänzen.
14. Anbieterneutrale Vorbereitung für OpenAI in Folge 16 sicherstellen.

---

# 6. Neue Fähigkeiten

Verwende stabile technische Schlüssel:

```text
text_generierung
bild_generierung
video_generierung
speech_to_text
text_to_speech
```

Die xAI-Integration deklariert nur Fähigkeiten, die mit dem konfigurierten Eintrag tatsächlich nutzbar sind.

Empfohlene spezifische Methoden:

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

Die tatsächlichen Signaturen dürfen an Folge 14 angepasst werden. Die fachlichen Fähigkeiten und ihre Trennung bleiben verbindlich.

Andere Module und das Testwerkzeug dürfen keine xAI-Klasse direkt aufrufen. Sie verwenden einen zentralen Fähigkeitsdienst, der Standard-Eintrag, deklarierte Fähigkeit, Berechtigung und Ergebnisnormalisierung behandelt.

Der Aufruf `integration_standard_aufrufen()` aus Folge 14 bleibt als kompatibler
Einstiegspunkt erhalten und delegiert an den neuen Fähigkeitsdienst. Bestehende
Aufrufer werden nicht gebrochen. Für neuen Code sind
`integration_faehigkeit_aufrufen()` beziehungsweise die Variante mit bewusst
gewähltem Eintrag zu verwenden. Es darf keine zweite parallele Aufrufarchitektur
entstehen.

---

# 7. Anbieterneutrale Anfragen und Ergebnisse

Definiere je Fähigkeit ein dokumentiertes normalisiertes Anfrageformat. Beispiele:

```php
[
    'prompt' => '...',
    'systemtext' => '...',
    'optionen' => [],
    'request_id' => '...',
]
```

```php
[
    'text' => 'Vorlesetext',
    'stimme_id' => 'eve',
    'sprache' => 'de',
    'format' => 'mp3',
]
```

Ein gemeinsames Ergebnis enthält mindestens:

```php
[
    'erfolg' => true,
    'status' => 'abgeschlossen',
    'faehigkeit' => 'bild_generierung',
    'anbieter' => 'xai',
    'integration_eintrag_id' => 7,
    'text' => '',
    'dateien' => [],
    'anbieter_auftrag_id' => '',
    'modell' => '',
    'nutzung' => [],
    'kosten' => [],
    'fehlercode' => '',
    'meldung' => '',
    'dauer_ms' => 0,
]
```

Zulässige Statuswerte mindestens:

```text
wartend
laeuft
abgeschlossen
fehlgeschlagen
abgebrochen
abgelaufen
```

Anbieter-Rohantworten dürfen zentral und größenbegrenzt für Diagnose gespeichert werden, sind aber nicht die Schnittstelle zu anderen Modulen.

---

# 8. xAI-Integration und Registrierung

Die Integration heißt sichtbar `xAI / Grok` und besitzt einen stabilen Schlüssel wie `xai_grok`.

Der Benutzer führt außerhalb von Noob2Claw aus:

1. https://console.x.ai/ öffnen.
2. Konto beziehungsweise Team einrichten.
3. Abrechnung und aktuelles Guthaben prüfen.
4. API-Key mit nur den benötigten Modell- und Endpunktberechtigungen erstellen.
5. Schlüssel einmal kopieren.
6. Schlüssel in Noob2Claw hinterlegen.

Es wird ein normaler Inference-API-Key verwendet, kein Management-Key. Der Key wird niemals im Klartext gespeichert, erneut angezeigt, in URLs geschrieben oder geloggt.

Integrationseinstellungen mindestens:

- frei wählbare Bezeichnung,
- API-Key als Secret,
- feste Basis-URL `https://api.x.ai/v1`,
- Textmodell,
- Bildmodell,
- Videomodell,
- Speech-to-Text-Konfiguration,
- Standardstimme und Sprache für Text-to-Speech,
- Timeout je Modalität,
- maximale Textausgabe,
- maximale Bildanzahl,
- maximale Videodauer beziehungsweise erlaubte Qualitätsstufen,
- maximale TTS-Zeichen,
- maximale STT-Dauer und Dateigröße,
- serverseitige Speicherung beim Anbieter erlauben: standardmäßig nein,
- Aktivstatus je Fähigkeit, falls einzelne Funktionen bewusst deaktiviert werden sollen.

Die Basis-URL ist für diese Klasse nicht frei editierbar. Eine spätere OpenAI-kompatible Integration kann eine eigene, kontrolliert validierte URL-Konfiguration erhalten.

---

# 9. xAI-Endpunkte

Nutze nach aktueller Dokumentation grundsätzlich:

```text
Text:            POST /v1/responses
Bilder:          POST /v1/images/generations
Videos starten:  POST /v1/videos/generations
Videostatus:     GET  /v1/videos/{request_id}
Speech-to-Text:  POST /v1/stt
Text-to-Speech:  POST /v1/tts
Stimmen:         GET  /v1/tts/voices
Modelle:         passende Modelllisten der xAI-API
```

Der Pfad wird nicht doppelt mit `/v1` zusammengesetzt. Authentifizierung erfolgt serverseitig über `Authorization: Bearer <API_KEY>`.

Bei der Textgenerierung wird Anbieter-Speicherung standardmäßig deaktiviert, sofern der aktuelle Endpunkt dies unterstützt. Benutzer müssen bewusst zustimmen, bevor Inhalte beim Anbieter länger gespeichert werden.

Alle Antworten werden auf Status, Content-Type, Größe und Struktur geprüft. Rate-Limits und temporäre Anbieterfehler werden als wiederholbar gekennzeichnet, nicht mit unkontrollierten Sofortschleifen beantwortet.

---

# 10. Modelle und Stimmen nicht fest verdrahten

Modellnamen verändern sich. Deshalb:

- verfügbare Modelle möglichst über dokumentierte xAI-Modellendpunkte laden,
- Ergebnis je Fähigkeit filtern,
- Auswahl im Integrationseintrag speichern,
- Zeitpunkt der letzten Aktualisierung anzeigen,
- zuletzt gültige Liste als Cache behalten,
- manuelle Aktion „Modelle aktualisieren“ anbieten,
- nicht mehr verfügbares gewähltes Modell deutlich markieren.

Beispiele aus der Dokumentation dürfen als Vorschlag dienen, nicht als unveränderliche Wahrheit:

```text
Text:  grok-4.6
Bild:  grok-imagine-image-2.0
Video: grok-imagine-video-1.5
```

Die aktuelle Verfügbarkeit für den konkreten API-Key entscheidet.

Die Stimmen liefert `stimmen()` als normalisiertes Array:

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

Interne IDs und sichtbare Namen werden getrennt behandelt. Die Liste wird gecacht und über „Stimmen aktualisieren“ erneuert.

---

# 11. Testwerkzeug der Integration

Die Detailverwaltung von `xAI / Grok` erhält einen eigenen Navigationspunkt `Testen`.

Das Testwerkzeug bietet fünf klar getrennte Modi:

```text
Text
Bild
Video
Text → Sprache
Sprache → Text
```

Gemeinsame Regeln:

- Auswahl des konkreten Integrationseintrags,
- Anzeige der tatsächlich verwendeten Standard- oder Eintragskonfiguration,
- kein API-Key in HTML oder JavaScript,
- CSRF- und Rechteprüfung,
- serverseitige Eingabevalidierung,
- Lade-, Erfolgs- und Fehlerzustand,
- Laufzeit, Modell und sichere Nutzungsdaten anzeigen,
- keine automatische Ausführung beim Seitenaufruf,
- kostenpflichtige Aktion klar kennzeichnen,
- Doppel-Klick und parallele identische Requests verhindern,
- Testergebnisse gehören nicht automatisch in produktive Inhalte.

## Texttest

- Prompt eingeben,
- optional Systemtext und Ausgabelimit,
- Text erzeugen,
- Ergebnis als sicher gerenderten Text beziehungsweise Markdown anzeigen,
- Kopieren ermöglichen.

## Bildtest

- Prompt eingeben,
- nur aktuell unterstützte Optionen anbieten,
- standardmäßig ein Bild erzeugen,
- Vorschau, Metadaten und sicheren Download anbieten.

## Videotest

- Prompt eingeben,
- nur erlaubte Dauer, Seitenverhältnis und Qualität anbieten,
- vor kostenintensiver Ausführung bestätigen,
- Auftrag starten und lokalen Status anzeigen,
- nicht im Browserrequest bis zur Fertigstellung blockieren,
- fertiges Video über die eigene geschützte Dateiauslieferung abspielen.

## Text-to-Speech-Test

- frei eingebbaren Beispieltext bereitstellen,
- Stimme aus `stimmen()` wählen,
- Sprache, Format und unterstützte Geschwindigkeit wählen,
- Button `Beispiel abspielen`,
- Audio erst nach Button-Klick generieren,
- Ergebnis im Audio-Player abspielen,
- Stoppen, erneut abspielen und sicher herunterladen ermöglichen,
- gleiche Anfrage optional kurzzeitig cachen, um unnötige Kosten zu vermeiden.

## Speech-to-Text-Test

- vorhandene Audiodatei hochladen oder Ton im Browser aufnehmen,
- Aufnahme starten, pausieren beziehungsweise stoppen und verwerfen,
- Dauer und Aufnahmezustand deutlich anzeigen,
- Aufnahme vor dem Upload lokal vorhören,
- anschließend bewusst `Transkribieren` auslösen,
- erkannten Text anzeigen und kopierbar machen,
- keine leere oder fehlgeschlagene Transkription als Erfolg melden.

---

# 12. Sichere Audioaufnahme im Browser

Verwende vorhandene JavaScript- und Upload-Strukturen. Wenn passend, nutze `MediaRecorder` mit vorheriger Feature-Erkennung.

Pflichtregeln:

- Aufnahme nur nach Benutzeraktion und Browserberechtigung,
- verständlicher Hinweis, wenn Mikrofon oder Browserfunktion nicht verfügbar ist,
- sichere HTTPS-Umgebung voraussetzen,
- unterstützten MIME-Typ im Browser ermitteln,
- Standardlimit für Dauer und Dateigröße,
- sichtbarer Aufnahmeindikator und Timer,
- Stoppen und Verwerfen jederzeit möglich,
- Mikrofonspuren nach Ende zuverlässig schließen,
- Datei serverseitig nach tatsächlichem MIME-Typ und Inhalt validieren,
- zufällige Dateinamen außerhalb direkt ausführbarer Bereiche,
- keine Verarbeitung des Originaldateinamens als Pfad,
- temporäre Aufnahmen nach konfigurierter Zeit löschen,
- Transkription nur an die gewählte Speech-to-Text-Integration senden.

Der Browser spricht niemals direkt mit xAI. Der API-Key bleibt ausschließlich auf dem Server.

---

# 13. Globale Standardeinstellungen erweitern

Ergänze die globale Integrationsauswahl um:

```text
Bildgenerierung
Textgenerierung
Videogenerierung
Speech-to-Text
Text-to-Speech
```

Technische Schlüssel:

```text
bild_generierung
text_generierung
video_generierung
speech_to_text
text_to_speech
```

Pro Fähigkeit werden nur aktive Integrationseinträge angezeigt, die diese Fähigkeit deklarieren und aktiviert haben. Jede Fähigkeit kann einen anderen Anbieter beziehungsweise Eintrag verwenden.

Beispiel:

```text
Textgenerierung  → xAI „Produktion“
Bildgenerierung  → xAI „Produktion“
Videogenerierung → kein Standard
Speech-to-Text   → anderer Anbieter
Text-to-Speech   → xAI „Produktion“
```

Es gibt keinen stillen Fallback auf irgendeinen Eintrag. Ein fehlender Standard erzeugt einen klaren, behandelbaren Zustand.

---

# 14. Stimme je Agent

Erweitere die Agentenverwaltung um eine anbieterneutrale Stimmenzuordnung.

In der Agentenmaske:

- nur Stimmen der aktuell gewählten globalen Text-to-Speech-Integration anzeigen,
- Stimmen über deren `stimmen()`-Methode laden,
- Option `Systemstandard` anbieten,
- gewählte Stimme mit stabiler ID speichern,
- Anbieter, Integrationseintrag und Anzeigename als nachvollziehbare Zuordnung sichern,
- Button `Beispiel abspielen` neben der Auswahl bereitstellen,
- freien Beispieltext oder einen sinnvollen vorbelegten Satz erlauben,
- Test-Audio erst nach Klick erzeugen und im Player abspielen.

Empfohlenes logisches Modell:

```text
agent_id
integration_eintrag_id
stimme_id
stimme_name_snapshot
erstellt_am
geaendert_am
```

Beim Wechsel der globalen Text-to-Speech-Integration:

- alte Zuordnung nicht blind mit dem neuen Anbieter verwenden,
- ungültige Stimme sichtbar markieren,
- bis zur Neuzuordnung kontrolliert den Systemstandard verwenden oder einen klaren Fehler liefern,
- keine zufällige Stimme auswählen.

Die Stimme wird in Folge 15 nur konfiguriert und getestet. Das automatische Vorlesen im Chat ist nicht Teil dieser Folge.

---

# 15. Asynchrone Videoaufträge

Videogenerierung liefert zunächst eine Anbieter-Auftrags-ID. Lege deshalb einen anbieterneutralen Medienauftrag an:

```text
id
integration_eintrag_id
faehigkeit
anbieter_auftrag_id
status
fortschritt
anfrage_json
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

Der in Folge 14 erstmals geschaffene zentrale Cron-Einstieg mit dem
Integrations-Dispatcher prüft offene Aufträge in sinnvollen Abständen. Erweitere
diesen vorhandenen Weg; baue weder eine zweite Cron-Datei noch einen parallelen
Scheduler.

Anforderungen:

- asynchrones Videopolling als eigener erlaubter Aufgabentyp im vorhandenen
  Integrations-Dispatcher aus Folge 14,
- begrenzte Polling-Frequenz und Laufzeit,
- atomarer Claim je Medienauftrag mit kontrollierter Ablaufzeit,
- begrenzter exponentieller Backoff und definierte maximale Gesamtdauer,
- klare terminale Statuswerte für Erfolg, Fehler, Abbruch und Ablauf,
- Statusabfrage nur über passenden Integrationseintrag,
- Fehlerisolierung je Auftrag,
- Abbruch beziehungsweise Ablauf festgefahrener Aufträge,
- UI pollt den lokalen Auftragsstatus, nicht unkontrolliert xAI,
- Wiederaufnahme nach Seitenwechsel,
- Schutz vor doppeltem Herunterladen oder Speichern.

---

# 16. Medien sicher speichern

Erzeugte Bilder, Videos und Audiodateien müssen dauerhaft in der vorhandenen geschützten Datei- beziehungsweise Uploadverwaltung gespeichert werden. Verlasse dich nicht auf temporäre Anbieter-URLs.

Beim serverseitigen Download:

- nur URLs beziehungsweise File-IDs akzeptieren, die direkt aus der validierten Anbieterantwort stammen,
- erlaubte Hosts in der xAI-Klasse begrenzen,
- Weiterleitungen erneut prüfen,
- private und lokale Netzbereiche blockieren,
- Timeout und maximale Dateigröße setzen,
- MIME-Typ und Dateisignatur prüfen,
- zufälligen lokalen Dateinamen verwenden,
- Endung aus validiertem Typ ableiten,
- keine ausführbaren Formate erlauben,
- Datei dem Benutzer, Testlauf und Integrationslauf zuordnen,
- Aufbewahrungszeit für Testmedien festlegen.

Auslieferung erfolgt über die bestehende geschützte Dateifunktion mit Rechteprüfung und sicheren Content-Headern.

---

# 17. Kosten und Missbrauch begrenzen

Generierung kann Kosten verursachen. Ergänze:

- globale und benutzerbezogene Rate-Limits,
- maximale Prompt- und Textlängen,
- höchstens eine kleine Standardzahl von Bildern,
- erlaubte Video-Dauern und Qualitätsstufen,
- maximale Aufnahme- und Audiodauer,
- Timeout und Auftragsablauf,
- Bestätigung vor Videogenerierung,
- Nutzungs- und Kostenmetadaten aus Anbieterantworten, wenn vorhanden,
- verständliche Hinweise ohne fest verdrahtete Preisversprechen.

Preise werden nicht als unveränderliche Werte in die Business-Logik eingebaut. Verlinke auf die aktuelle Anbieterdokumentation.

---

# 18. Rechte und Sicherheit

Nutze passende vorhandene Rechte oder ergänze idempotent sinngemäß:

```text
integrationen_ki_testen
integrationen_modelle_aktualisieren
integrationen_stimmen_aktualisieren
integrationen_medien_generieren
integrationen_audio_transkribieren
integrationen_medien_anzeigen
agenten_stimme_verwalten
```

Verbindlich:

- Administratoren erhalten neue Rechte, andere Rollen nicht automatisch.
- Schreibende Aktionen benötigen CSRF-Schutz.
- API-Key wird zentral verschlüsselt.
- Secrets werden maskiert und nie an Browser-JavaScript geliefert.
- Requests nutzen ausschließlich HTTPS und fest erlaubte Anbieterziele.
- Eingaben, Optionen, Uploads und Anbieterantworten werden serverseitig validiert.
- SQL ist parametrisiert; HTML-Ausgaben werden escaped.
- Prompts und Transkripte werden nicht unnötig in allgemeinen Logs gespeichert.
- Datenschutz- und Aufbewahrungseinstellungen sind sichtbar.
- Provider-Contentfilter und Moderationsfehler werden als eigene sichere Status behandelt.
- Dateizugriffe benötigen Authentifizierung und Objektberechtigung.

---

# 19. Datenbank und Migrationen

Erweitere vorhandene Tabellen aus Folge 14 statt sie zu duplizieren. Zusätzlich können erforderlich sein:

- Cache für Modelle und Stimmen,
- anbieterneutrale Generierungs- beziehungsweise Medienaufträge,
- Zuordnung Agent zu TTS-Stimme,
- lokale Medienreferenzen und Aufbewahrungsstatus,
- Nutzungs- und Kostenmetadaten ohne sensible Inhalte.

Alle Migrationen sind mehrfach ausführbar, nicht destruktiv und besitzen sichere Standardwerte. Kein `DROP TABLE`, kein `TRUNCATE` und kein Überschreiben bestehender Integrationseinträge.

---

# 20. Testfälle

Teste mindestens:

## Anbieterneutral

- alle fünf Fähigkeiten erscheinen in den globalen Standards,
- nur passende aktive Einträge sind auswählbar,
- jede Fähigkeit kann auf einen anderen Eintrag zeigen,
- fehlender Standard wird sauber behandelt,
- Testwerkzeug ruft den zentralen Fähigkeitsdienst auf und keine xAI-Klasse direkt,
- System funktioniert ohne xAI-Key im Zustand „nicht konfiguriert“.

## xAI

- gültiger API-Key und fehlender beziehungsweise ungültiger API-Key,
- Modell- und Stimmenlisten laden, cachen und aktualisieren,
- ausgewähltes nicht mehr verfügbares Modell wird erkannt,
- Text-, Bild- und TTS-Erzeugung,
- STT per Upload und Browseraufnahme,
- asynchroner Videoauftrag bis Abschluss,
- Rate-Limit, Timeout, ungültige Antwort und Contentfilter-Fehler,
- API-Key erscheint weder in HTML noch in Logs.

## Testwerkzeug

- jeder Modus besitzt passende Felder und Ergebnisse,
- Doppel-Klick erzeugt keinen unbeabsichtigten Doppelauftrag,
- frei eingegebener TTS-Beispieltext wird mit gewählter Stimme abgespielt,
- Audioaufnahme zeigt Zustand und Dauer,
- Mikrofonablehnung und nicht unterstützter Browser werden verständlich behandelt,
- Aufnahme kann vor Upload vorgehört und verworfen werden,
- temporäre Dateien werden bereinigt.

## Agentenstimmen

- Stimmenliste stammt aus der gewählten TTS-Integration,
- Stimme lässt sich jedem Agenten zuordnen,
- `Systemstandard` funktioniert,
- Beispieltext lässt sich aus der Agentenmaske abspielen,
- Wechsel der TTS-Standardintegration markiert unpassende Zuordnungen,
- keine zufällige Stimme wird stillschweigend gewählt.

## Medien und Hintergrundaufträge

- Bild, Video und Audio werden lokal und geschützt gespeichert,
- ungültige MIME-Typen und zu große Dateien werden abgewiesen,
- temporäre Anbieter-URL wird nicht als dauerhafte Dateireferenz verwendet,
- Video-Polling besitzt Begrenzung, Fehlerisolierung und Ablauf,
- geschützte Medien sind ohne Berechtigung nicht abrufbar.

---

# 21. Ausdrücklich nicht Bestandteil dieser Folge

- Audioaufnahme im bestehenden Chat,
- automatische Übernahme von Transkripten in Chatnachrichten,
- automatisches Vorlesen neuer Agentenantworten,
- Ersetzen der bisherigen Agenten-Chatkommunikation durch direkte Textgenerierung,
- OpenAI-Integrationsklasse – sie folgt in Folge 16,
- beliebige frei konfigurierbare OpenAI-kompatible Hosts innerhalb der xAI-Klasse.

Bereite Schnittstellen so vor, dass diese Erweiterungen später ohne Umbau der grundlegenden Architektur möglich sind. Implementiere sie jetzt noch nicht.

---

# 22. Entwicklungsreihenfolge

1. Bestand aus Folge 14 und Agentenverwaltung analysieren.
2. Fähigkeiten und normalisierte Verträge definieren.
3. Migrationen und zentrale Fähigkeitsfunktionen erstellen.
4. xAI-Klasse und sichere Konfiguration implementieren.
5. Modell- und Stimmenabruf umsetzen.
6. globalen Standarddialog erweitern.
7. Testwerkzeug je Modalität aufbauen.
8. Browseraufnahme und STT-Test ergänzen.
9. asynchrone Videoaufträge und lokale Medienspeicherung ergänzen.
10. Stimmenzuordnung und Abspieltest in der Agentenverwaltung umsetzen.
11. Rechte, Limits, Fehlerbehandlung und Logs prüfen.
12. alle Testfälle durchführen und dokumentieren.

Nach jedem Schritt PHP-Syntax, Browseransicht, Datenbankzustand, Rechte und Logs prüfen.

---

# 23. Abschlussbericht

Dokumentiere:

- wiederverwendete Strukturen aus Folge 14,
- neue und geänderte Dateien,
- Migrationen,
- neue Fähigkeiten und Methoden,
- normalisierte Anfrage- und Ergebnisformate,
- xAI-Konfiguration und verwendete Endpunkte,
- aktuelle verwendete Modelle ohne Garantie für spätere Verfügbarkeit,
- Secret-, Datei- und Datenschutzkonzept,
- Aufbau des Testwerkzeugs,
- Audioaufnahme und Transkription,
- Video-Auftragsablauf,
- globale Standards,
- Stimmenliste und Agentenzuordnung,
- Rechte und Limits,
- ausgeführte Tests und Ergebnisse,
- ausdrücklich zurückgestellte Chat-Integration,
- Vorbereitung für OpenAI in Folge 16.

Die Aufgabe ist abgeschlossen, wenn alle fünf Fähigkeiten anbieterneutral definiert, mit xAI testbar, global auswählbar und sicher verwaltet sind, Stimmen je Agent zugeordnet und mit eigenem Beispieltext getestet werden können und keinerlei Chat-Integration aus späteren Folgen vorweggenommen wurde.
