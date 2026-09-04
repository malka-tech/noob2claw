# Noob2Claw – Folge 17: Startprompt
# Speech-to-Text und Text-to-Speech im vorhandenen Chat

Du arbeitest am bestehenden Projekt Noob2Claw.

In Folge 14 wurde die allgemeine Integrationsverwaltung geschaffen. In Folge 15 kamen die anbieterneutralen Fähigkeiten `speech_to_text` und `text_to_speech`, das Integrationstestwerkzeug sowie die Stimmenzuordnung je Agent hinzu. In Folge 16 wurde bewiesen, dass mehrere Anbieter dieselben Fähigkeiten bereitstellen können.

Erweitere jetzt den vorhandenen Chat, sodass Benutzer Nachrichten per Mikrofon erfassen und neue Agentenantworten auf Wunsch vorlesen lassen können.

Der Chat muss vollständig anbieterneutral bleiben. Er darf weder die Grok- noch die OpenAI-Klasse direkt aufrufen.

---

# 1. Repository und verbindliche Grundlagen

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

Aktualisiere die Vorlage vorsichtig und bewahre vorhandene lokale Arbeit. Lies anschließend vollständig:

- sämtliche Dokumente unter `docs/folge_9_framework/`,
- Folge 10 für Agentenverwaltung und Agentendaten,
- Folge 14 für Integrationsklassen, Einträge, globale Standards und Cronjobs,
- Folge 15 für Sprachfähigkeiten, Stimmen, Testwerkzeug und Medienaufträge,
- Folge 16 für die zweite Anbieterintegration,
- `docs/folge_17_chat_sprache/1_Chat_Speech_to_Text_und_Text_to_Speech.md` als primäre Aufgabenbeschreibung.

Die vorhandenen Architektur-, Datenbank-, Rechte-, API-, Formular-, Upload-, Datei-, Sicherheits- und Logging-Standards sind verbindlich.

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

- bestehender Chatoberfläche und Nachrichten-API,
- Chat-, Nachrichten-, Benutzer- und Agenten-IDs,
- vorhandener Streaming- oder Polling-Logik für Agentenantworten,
- zentralem Fähigkeitsdienst,
- globalen Standards für `speech_to_text` und `text_to_speech`,
- Stimmenvertrag `stimmen()` und Agenten-Stimmenzuordnung,
- geschützter Datei- und Medienverwaltung,
- Generierungs- beziehungsweise Medienaufträgen,
- CSRF-, Rechte-, Session- und Objektberechtigungsprüfungen,
- zentralem HTTP-Client und Secret-Verwaltung,
- bestehenden Aufräum- und Cronjobmechanismen.

Erweitere die vorhandenen Strukturen. Baue kein paralleles Chatsystem, keine zweite Integrationsauswahl, keine zweite Dateiablage und keine neue Anbieter-Sonderlogik im Chat.

---

# 3. Pflichtziele

1. Im vorhandenen Chat einen klaren Mikrofon- beziehungsweise Aufnahmebutton ergänzen.
2. Ton erst nach einer bewussten Benutzeraktion aufnehmen.
3. Aufnahmezustand, Dauer, Stoppen und Verwerfen sichtbar machen.
4. Die Aufnahme vor der Übertragung lokal abspielbar machen.
5. Audio sicher an den Server übertragen und dort validieren.
6. Die global eingestellte Speech-to-Text-Integration über den zentralen Fähigkeitsdienst aufrufen.
7. Den erkannten Text in das vorhandene Chat-Eingabefeld übernehmen.
8. Den Text vor dem Senden korrigierbar lassen.
9. Die bestätigte Transkription anschließend über den vorhandenen Textnachrichtenweg senden.
10. Im Chat einen Schalter `Neue Agentenantworten vorlesen` ergänzen.
11. Diese Einstellung benutzer- und chatbezogen speichern.
12. Nur neue, vollständig abgeschlossene Agentenantworten automatisch vorlesen.
13. Für Text-to-Speech den globalen Standard und die konfigurierte Agentenstimme verwenden.
14. Bei blockierter automatischer Wiedergabe einen manuellen Abspielknopf anbieten.
15. Manuelles Abspielen, Pausieren beziehungsweise Stoppen und erneutes Abspielen ermöglichen.
16. Die Agentenmaske so vervollständigen, dass je Agent eine Stimme aus der aktiven Text-to-Speech-Integration gewählt und getestet werden kann.
17. Ungültige Stimmenzuordnungen nach einem Integrationswechsel sichtbar und sicher behandeln.
18. Kosten, Doppelaufrufe, Dateigrößen, Aufnahmedauer, Datenschutz und Rechte begrenzen.
19. Anbieterneutrale Tests für Grok, OpenAI und weitere passende Integrationen ermöglichen.

---

# 4. Anbieterneutraler Aufruf

Der Chat verwendet nur die bestehenden Fähigkeitsschlüssel:

```text
speech_to_text
text_to_speech
```

Der Ablauf erfolgt sinngemäß über einen zentralen Dienst:

```php
$ergebnis = integrations_faehigkeit_ausfuehren(
    'speech_to_text',
    $anfrage,
    $kontext
);
```

```php
$ergebnis = integrations_faehigkeit_ausfuehren(
    'text_to_speech',
    $anfrage,
    $kontext
);
```

Die tatsächlichen Funktionsnamen werden an den vorhandenen Projektstand angepasst.

Der zentrale Dienst muss mindestens:

- den globalen Standard für die Fähigkeit auflösen,
- aktiven Integrationseintrag und deklarierte Fähigkeit prüfen,
- Benutzer- und Objektberechtigung prüfen,
- die Anbieterklasse ausschließlich über die Registry laden,
- normalisierte Anfrage und Antwort verwenden,
- sichere Fehlercodes zurückgeben,
- Nutzung, Laufzeit und notwendige Audit-Metadaten erfassen,
- niemals API-Keys an Browser oder Chatcode liefern.

Es gibt keinen stillen Fallback auf irgendeinen verfügbaren Anbieter.

---

# 5. Aufnahmeablauf im Chat

Der Standardablauf ist bewusst kontrollierbar:

```text
Aufnahme starten
      ↓
Aufnahme stoppen
      ↓
lokal vorhören oder verwerfen
      ↓
transkribieren
      ↓
erkannten Text im Eingabefeld prüfen und bearbeiten
      ↓
über vorhandenen Senden-Button abschicken
```

Der erkannte Text wird nicht ungeprüft automatisch gesendet. Dadurch kann der Benutzer Eigennamen, Zahlen und Erkennungsfehler korrigieren.

Die Aufnahme ist in dieser Folge nur die Quelle der Transkription. Sie wird nicht automatisch als dauerhafte Audionachricht in den Chatverlauf aufgenommen und nicht direkt an den Agenten weitergereicht.

---

# 6. Oberfläche für Speech-to-Text

Ergänze im vorhandenen Nachrichten-Composer:

- einen beschrifteten Mikrofonbutton,
- Zustand `bereit`, `Berechtigung wird angefragt`, `nimmt auf`, `pausiert`, `wird verarbeitet`, `fertig` oder `Fehler`,
- sichtbaren Aufnahmetimer,
- Buttons für Pause/Fortsetzen, Stoppen und Verwerfen, sofern technisch sinnvoll,
- lokale Audiovorschau nach dem Stoppen,
- Button `Transkribieren`,
- Fortschritts- und Fehleranzeige,
- klaren Hinweis, welche Speech-to-Text-Integration verwendet wird,
- verständlichen Zustand, wenn kein Standard konfiguriert ist.

Nach erfolgreicher Transkription:

- erkannten Text in das vorhandene Eingabefeld einsetzen,
- vorhandenen Text nicht unbemerkt überschreiben,
- bei bereits vorhandenem Text eine bewusste Entscheidung `Anhängen` oder `Ersetzen` ermöglichen,
- Fokus in das Eingabefeld setzen,
- Benutzer kann den Text bearbeiten,
- Senden erfolgt über den bestehenden Nachrichtenweg.

Verhindere Doppel-Klicks und parallele Transkriptionsaufrufe derselben Aufnahme.

---

# 7. Browseraufnahme

Verwende nach Feature-Erkennung die vorhandenen Browser-APIs, insbesondere `navigator.mediaDevices.getUserMedia()` und `MediaRecorder`.

Pflichtregeln:

- Mikrofonzugriff nur nach Klick oder Tastaturaktion anfordern.
- Für Mikrofonzugriff HTTPS beziehungsweise einen sicheren Kontext voraussetzen.
- `navigator.mediaDevices`, `getUserMedia` und `MediaRecorder` vor Nutzung prüfen.
- Aufnahmeformat mit `MediaRecorder.isTypeSupported()` aus einer kleinen erlaubten Liste auswählen.
- Kein bestimmtes Format auf allen Browsern voraussetzen.
- MIME-Typ, Dateigröße und Dauer client- und serverseitig begrenzen.
- leere Chunks beziehungsweise leere Aufnahmen nicht übertragen.
- sichtbaren Aufnahmeindikator und barrierefreie Statusmeldung anzeigen.
- alle Tracks nach Stoppen, Verwerfen, Fehler und Seitenwechsel mit `track.stop()` beenden.
- erzeugte Object-URLs mit `URL.revokeObjectURL()` wieder freigeben.
- Mikrofonablehnung, fehlendes Gerät, belegtes Gerät und Browserfehler verständlich behandeln.
- bei nicht unterstützter Aufnahme eine vorhandene Audiodatei als barrierearmen Fallback hochladen lassen, sofern die Uploadarchitektur dies erlaubt.

Offizielle Browserreferenzen:

```text
https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia
https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder
https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder/isTypeSupported_static
```

---

# 8. Serverseitige Verarbeitung der Aufnahme

Die Browseraufnahme wird als kontrollierter Upload an den eigenen Server übertragen. Der Browser spricht niemals direkt mit einer Anbieter-API.

Serverseitig zwingend:

- authentifizierte Session,
- CSRF-Schutz,
- Berechtigung zur Nutzung des konkreten Chats,
- Begrenzung von Request- und Dateigröße,
- Prüfung des tatsächlichen Dateiinhalts und MIME-Typs,
- zufälliger Dateiname ohne Verwendung des Originalnamens als Pfad,
- Ablage außerhalb direkt ausführbarer Bereiche,
- keine ausführbaren Dateiformate,
- konfigurierbares Timeout,
- temporäre Datei mit Ablaufzeit,
- Übergabe nur an die gewählte Speech-to-Text-Integration,
- Löschung der temporären Aufnahme nach erfolgreicher Verarbeitung oder kurzer Fehlerfrist,
- keine vollständigen Transkripte oder Audiodaten in allgemeinen Logs.

Die Speech-to-Text-Anfrage enthält sinngemäß:

```php
[
    'datei_id' => 123,
    'sprache' => null,
    'chat_id' => 42,
    'benutzer_id' => 7,
    'request_id' => '...',
]
```

Die Integration liefert das normalisierte Ergebnis aus Folge 15. Verwende das Textfeld nur bei `erfolg = true` und gültigem Abschlussstatus.

---

# 9. Übernahme als Chatnachricht

Die Transkription ist zunächst ein Entwurf und noch keine Nachricht.

Nach `Transkribieren`:

1. Ergebnis sicher als reinen Text übernehmen.
2. vorhandenen Composer-Inhalt respektieren.
3. Benutzer bearbeiten oder verwerfen lassen.
4. erst nach dem vorhandenen Senden-Vorgang eine Chatnachricht anlegen.
5. genau denselben Nachrichten- und Agentenablauf wie bei getipptem Text verwenden.

Die gespeicherte Chatnachricht enthält den bestätigten Text. Optional dürfen minimale technische Metadaten nachvollziehbar verknüpft werden:

```text
Eingabeart: Sprache
Speech-to-Text-Integrationseintrag
verwendetes Modell
Dauer der Aufnahme
Zeitpunkt
```

Keine Anbieter-Rohantwort, kein Secret und keine temporäre Dateiposition gehören in die Nachricht.

---

# 10. Vorlesen im Chat aktivieren

Ergänze im vorhandenen Chat einen verständlichen Schalter:

```text
Neue Agentenantworten vorlesen
```

Regeln:

- standardmäßig ausgeschaltet,
- wird nur nach bewusster Benutzeraktion eingeschaltet,
- Zustand pro Benutzer und aktuellem Chat gespeichert,
- falls es im Projekt keine stabilen Chat-Instanzen gibt, ersatzweise pro Benutzer und Agent speichern,
- Einstellung anderer Benutzer nicht beeinflussen,
- sichtbarer aktiver und inaktiver Zustand,
- Tastaturbedienung und passende ARIA-Beschriftung,
- Hinweis auf die verwendete Text-to-Speech-Integration und Agentenstimme,
- klarer Zustand bei fehlendem TTS-Standard oder ungültiger Stimme.

Beim Ausschalten:

- laufende automatische Wiedergabe stoppen,
- noch nicht gestartete automatische Einträge aus der lokalen Warteschlange entfernen,
- bereits erzeugte berechtigte Audiodateien dürfen für manuelles erneutes Abspielen erhalten bleiben,
- keine neuen automatischen TTS-Anfragen mehr erzeugen.

---

# 11. Welche Nachrichten vorgelesen werden

Automatisch vorgelesen werden ausschließlich:

- neue Agentennachrichten,
- die nach Aktivierung des Schalters eintreffen,
- die vollständig abgeschlossen und im Chat gespeichert sind,
- deren sichtbarer Nachrichtentext nicht leer ist,
- für die der aktuelle Benutzer Leseberechtigung besitzt.

Nicht automatisch vorgelesen werden:

- alte Nachrichten beim Öffnen oder Neuladen des Chats,
- Benutzer- oder Systemnachrichten,
- Zwischenstände einer gestreamten Antwort,
- versteckte Metadaten, Toolausgaben oder interne Anweisungen,
- Fehlermeldungen ohne bewusst dafür vorgesehenen sichtbaren Text,
- dieselbe Nachricht mehrfach nach Reconnect oder Polling.

Jede geeignete Agentennachricht erhält zusätzlich einen manuellen Button `Vorlesen` beziehungsweise `Abspielen`. Dadurch können alte Nachrichten bewusst gehört und blockierte Autoplay-Versuche nachgeholt werden.

---

# 12. Sprechtext vorbereiten

Übergebe nicht blind den gesamten gespeicherten Rohinhalt an Text-to-Speech.

Erzeuge aus der sichtbaren Agentenantwort einen sicheren, nachvollziehbaren Sprechtext:

- Markdown-Steuerzeichen entfernen,
- Linkbeschriftung sprechen und lange URLs standardmäßig auslassen,
- Bilder, versteckte Inhalte und HTML nicht vorlesen,
- Codeblöcke standardmäßig überspringen oder kurz als „Codeblock“ ankündigen,
- Listen und Überschriften in natürliche Pausen übersetzen,
- wiederholte Leerzeichen normalisieren,
- maximale Länge anwenden,
- Kürzung sichtbar kennzeichnen und nie mitten in sensiblen Daten unkontrolliert abbrechen,
- exakt den daraus erzeugten Sprechtext hashen, damit identische TTS-Aufträge erkannt werden.

Die normale Textdarstellung im Chat bleibt unverändert.

---

# 13. Stimme je Agent

Vervollständige die vorhandene Agentenmaske aus Folge 15. Erfinde keine zweite Stimmenverwaltung.

Die Maske zeigt:

- aktuell global gewählte Text-to-Speech-Integration,
- konkreten Integrationseintrag,
- Stimmen aus dessen normalisierter Methode `stimmen()`,
- Option `Systemstandard`,
- gespeicherte Agentenstimme,
- verständliche Warnung bei ungültiger oder nicht mehr verfügbarer Stimme,
- freien Beispieltext,
- Button `Beispiel abspielen`.

Speichere beziehungsweise verwende mindestens die logische Zuordnung:

```text
agent_id
integration_eintrag_id
stimme_id
stimme_name_snapshot
geaendert_am
```

Auswahlreihenfolge bei Text-to-Speech:

1. gültige Stimme des Agenten für den aktuell gewählten TTS-Integrationseintrag,
2. ausdrücklich konfigurierte Standardstimme dieses Integrationseintrags,
3. klarer Fehlerzustand.

Wähle niemals stillschweigend eine zufällige Stimme. Stimmen-IDs verschiedener Anbieter gelten nicht als austauschbar, auch wenn sie gleich geschrieben werden.

Beim Wechsel der globalen TTS-Integration bleibt die alte Zuordnung als Historie erhalten, wird aber sichtbar als nicht passend markiert und nicht an den neuen Anbieter gesendet.

---

# 14. Text-to-Speech-Ablauf

Sobald eine neue Agentenantwort vollständig gespeichert ist und der Benutzer das Vorlesen aktiviert hat:

```text
neue vollständige Agentennachricht
      ↓
Idempotenz und Berechtigung prüfen
      ↓
globalen TTS-Integrationseintrag auflösen
      ↓
gültige Agenten- oder Standardstimme auflösen
      ↓
Sprechtext erzeugen
      ↓
Text-to-Speech über Fähigkeitsdienst
      ↓
Audio geschützt speichern
      ↓
Wiedergabe versuchen
```

Nutze vorhandene Medien- beziehungsweise Auftragsstrukturen aus Folge 15. Wenn die Integration synchron antwortet, darf das Ergebnis sofort gespeichert werden. Wenn sie asynchron arbeitet, verwendet die UI den vorhandenen lokalen Auftragsstatus. Baue keinen zweiten Jobmechanismus.

Ein eindeutiger Schlüssel verhindert Doppelgenerierung, sinngemäß aus:

```text
nachricht_id
integration_eintrag_id
stimme_id
sprechtext_hash
ausgabeformat
```

Dasselbe berechtigte Audio darf wiederverwendet werden. Bei geändertem Nachrichtentext, Anbieter, Stimme oder Format entsteht ein neuer Schlüssel.

---

# 15. Wiedergabe und Browser-Autoplay

Browser können skriptgestartete Audiowiedergabe blockieren. Behandle das als normalen UI-Zustand.

Pflichtverhalten:

- Aktivierung des Vorleseschalters erfolgt durch eine Benutzeraktion.
- `audio.play()` wird als Promise behandelt.
- Bei erfolgreicher Wiedergabe wird der echte Zustand angezeigt.
- Bei `NotAllowedError` erscheint deutlich `Audio ist bereit – zum Abspielen klicken`.
- Es gibt immer manuelle Wiedergabesteuerung.
- Mehrere Antworten spielen nicht gleichzeitig.
- Neue Audios landen in einer geordneten lokalen Warteschlange.
- Stoppen beendet die aktuelle Wiedergabe und leert auf Wunsch die Warteschlange.
- Beim Wechsel von Chat oder Agent wird die Wiedergabe beendet.
- Hintergrund-Tabs erzeugen keine unkontrollierte Geräuschkulisse.
- Lautstärke wird als Benutzerpräferenz behandelt, wenn das bestehende Einstellungssystem dies unterstützt.

Offizielle Browserreferenzen:

```text
https://developer.mozilla.org/en-US/docs/Web/Media/Guides/Autoplay
https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play
```

---

# 16. API-Aktionen

Erweitere die vorhandene Chat-API nach deren Konventionen. Benötigte Aktionen sinngemäß:

```text
chat_audio_transkribieren
chat_vorlesen_einstellung_speichern
chat_tts_anfordern
chat_tts_status
chat_audio_ausliefern
```

Nutze vorhandene Aktionen, wenn sie diese Aufgaben bereits abdecken. Keine parallelen Endpunkte ohne Notwendigkeit.

Jede Aktion prüft:

- HTTP-Methode,
- Authentifizierung,
- CSRF bei schreibenden Aufrufen,
- allgemeines Recht,
- konkrete Chat-, Nachrichten- und Dateiobjektberechtigung,
- Eingabetypen und Größen,
- globalen Integrationsstandard,
- deklarierte Fähigkeit,
- Idempotenz beziehungsweise Wiederholung,
- sichere Fehlerausgabe.

Eine Nachrichten-ID darf nur verwendet werden, wenn sie zum aufgerufenen Chat gehört und der Benutzer die Nachricht sehen darf.

---

# 17. Datenmodell und Migrationen

Erweitere bestehende Tabellen und Medienaufträge. Erzeuge keine Duplikate zu Folge 15.

Zusätzlich kann eine Chat-Vorleseeinstellung erforderlich sein:

```text
id
benutzer_id
chat_id
vorlesen_aktiv
lautstaerke optional
erstellt_am
geaendert_am
```

Für erzeugte Sprachausgaben werden vorhandene Medien- oder Generierungsaufträge mit der Chatnachricht verknüpft. Logisch erforderlich:

```text
nachricht_id
integration_eintrag_id
stimme_id
sprechtext_hash
datei_id
status
erstellt_am
ablauf_am optional
```

Für Transkriptionen genügen vorhandene Audit- beziehungsweise Nutzungsstrukturen. Eine dauerhafte Speicherung der Rohaufnahme ist standardmäßig nicht erforderlich.

Folge 11 stellt mit `agenten_chats` bereits stabile Chat-IDs bereit. Verwende
daher ausschließlich `chat_id`; eine Speicherung pro Benutzer-Agent-Paar würde
bei mehreren Chats Einstellungen vermischen. API-Aufrufe folgen durchgehend der
bestehenden Form `modul=agenten_chat&aktion=...` und verwenden `nachricht_id`
statt neu eingeführter englischer Bezeichner.

Alle Migrationen:

- sind mehrfach ausführbar,
- besitzen sichere Standardwerte,
- verändern bestehende Chatnachrichten nicht destruktiv,
- löschen keine Stimmenzuordnungen,
- verwenden eindeutige Indizes gegen Doppelaufträge,
- enthalten kein `DROP TABLE` oder `TRUNCATE`.

---

# 18. Rechte und Datenschutz

Verwende vorhandene Chatrechte oder ergänze idempotent sinngemäß:

```text
chat_audio_aufnehmen
chat_audio_transkribieren
chat_nachrichten_vorlesen
agenten_stimme_verwalten
```

Verbindlich:

- Benutzer dürfen Audio nur in Chats verwenden, auf die sie Zugriff haben.
- Mikrofonzugriff wird verständlich angekündigt und erst nach Benutzeraktion angefordert.
- Der verwendete externe Sprachdienst muss in der Oberfläche nachvollziehbar sein.
- Audio und Text werden nur für den beabsichtigten Integrationsaufruf übertragen.
- temporäre Aufnahmen erhalten kurze, konfigurierbare Aufbewahrungszeiten.
- Inhalte werden nicht unnötig in Logs gespeichert.
- API-Keys bleiben ausschließlich auf dem Server.
- geschützte Audiodateien werden nur nach Authentifizierung und Objektberechtigung ausgeliefert.
- Downloadantworten erhalten sicheren Content-Type, `nosniff` und passende Cache-Header.
- Rate-Limits gelten pro Benutzer und Fähigkeit.
- Administratoren erhalten neue Verwaltungsrechte; andere Rollen nicht automatisch.

---

# 19. Kosten und Last begrenzen

Speech-to-Text und Text-to-Speech können kostenpflichtig sein.

Ergänze:

- maximale Aufnahmedauer,
- maximale Uploadgröße,
- erlaubte Audioformate,
- maximale Länge des Sprechtexts,
- serverseitige Timeouts,
- Rate-Limits pro Benutzer und Chat,
- Schutz gegen Doppel-Klicks,
- idempotente TTS-Aufträge,
- Wiederverwendung identischer TTS-Dateien,
- keine automatische Generierung alter Nachrichten beim Laden,
- keine Generierung leerer oder nur aus Markup bestehender Texte,
- begrenzte lokale Wiedergabewarteschlange,
- Aufräumen abgelaufener temporärer Aufnahmen und Testdateien über vorhandene Jobs.

Feste Anbieterpreise gehören nicht in die Business-Logik.

---

# 20. Barrierefreiheit und Bedienbarkeit

- Mikrofon, Stoppen, Verwerfen, Transkribieren, Vorlesen, Pause und Stop besitzen sichtbare Texte oder eindeutige zugängliche Namen.
- Alle Funktionen sind per Tastatur bedienbar.
- Aufnahme- und Verarbeitungsstatus werden über eine passende `aria-live`-Region angekündigt.
- Farbe ist nie der einzige Zustandsindikator.
- Der Timer aktualisiert sich visuell, ohne Screenreader unnötig jede Sekunde zu überlasten.
- Die Transkription bleibt immer auch als Text verfügbar.
- Automatisches Vorlesen ist abschaltbar.
- Ein globales Stoppen der Audioausgabe ist jederzeit erreichbar.
- Fokusführung nach Berechtigungsfehler, Transkription und Wiedergabefehler ist nachvollziehbar.

---

# 21. Fehlerzustände

Mindestens verständlich behandeln:

```text
kein Speech-to-Text-Standard
kein Text-to-Speech-Standard
Integration inaktiv
Fähigkeit nicht vorhanden
Mikrofon nicht erlaubt
kein Mikrofon gefunden
Browseraufnahme nicht unterstützt
Aufnahme leer oder zu lang
Dateiformat ungültig
Transkription leer
Anbieter-Timeout oder Rate-Limit
Agentenstimme nicht mehr verfügbar
Audiogenerierung fehlgeschlagen
automatische Wiedergabe blockiert
Nachricht oder Chat nicht berechtigt
```

Autoplay-Blockierung ist kein roter Systemfehler. Das Audio bleibt bereit und kann manuell gestartet werden.

---

# 22. Testfälle

## Speech-to-Text

- Aufnahme startet nur nach Benutzeraktion.
- Berechtigung akzeptiert, abgelehnt und unbeantwortet.
- kein Mikrofon und belegtes Gerät.
- unterstützte Formate werden dynamisch erkannt.
- Start, Pause, Fortsetzen, Stoppen und Verwerfen.
- Tracks werden in jedem Endzustand beendet.
- lokale Vorschau funktioniert.
- leere, zu lange, zu große und manipulierte Datei wird abgewiesen.
- kein globaler STT-Standard ergibt klare Meldung.
- aktive Grok-, OpenAI- oder andere passende Integration funktioniert über denselben Dienst.
- Transkript überschreibt vorhandenen Composer-Text nicht unbemerkt.
- Benutzer kann das Transkript ändern und normal senden.
- doppelte Transkription derselben Aufnahme wird verhindert.
- temporäre Aufnahme wird fristgerecht gelöscht.

## Text-to-Speech

- Schalter ist zunächst aus.
- Einstellung wird nur für den aktuellen Benutzer und Chat gespeichert.
- nur neue vollständige Agentennachrichten werden automatisch verarbeitet.
- alte Nachrichten starten beim Laden nicht.
- Benutzer-, System- und Zwischenmeldungen werden nicht vorgelesen.
- Markdown und Code werden korrekt in Sprechtext umgewandelt.
- gültige Agentenstimme wird verwendet.
- Integration-Standardstimme wird nur als ausdrücklich konfigurierte Rückfalloption verwendet.
- ungültige Stimme erzeugt Warnung statt zufälliger Auswahl.
- doppelte Events erzeugen keinen zweiten kostenpflichtigen Auftrag.
- mehrere Audios überlappen nicht.
- Autoplay-Erfolg und `NotAllowedError` werden korrekt behandelt.
- manueller Abspiel-, Pause-, Stop- und Wiederholen-Button funktioniert.
- Ausschalten beendet Wiedergabe und verhindert neue automatische Aufträge.
- geschützte Audiodatei ist ohne Berechtigung nicht erreichbar.

## Agentenmaske

- Stimmen stammen aus der aktuell globalen TTS-Integration.
- Anbieter und Integrationseintrag sind sichtbar.
- `Systemstandard` funktioniert.
- eigener Beispieltext kann abgespielt werden.
- Wechsel des globalen TTS-Standards markiert alte Zuordnung als unpassend.
- gleich benannte Stimmen verschiedener Anbieter werden nicht verwechselt.
- fehlende oder leere Stimmenliste wird sauber behandelt.

## Regression

- getippte Nachrichten funktionieren unverändert.
- vorhandenes Chat-Streaming beziehungsweise Polling funktioniert weiter.
- Grok- und OpenAI-Integrationstestwerkzeug funktioniert weiter.
- globale Integrationsstandards bleiben verwaltbar.
- bestehende Agenten ohne Stimmenzuordnung verursachen keinen Absturz.
- Benutzer ohne neue Rechte sehen beziehungsweise verwenden die Funktionen nicht unberechtigt.

---

# 23. Ausdrücklich nicht Bestandteil dieser Folge

- dauerhafte Sprachnachrichten als eigener Chatnachrichtentyp,
- direkte Übergabe der Audiodatei an das Agentenmodell,
- Echtzeit-Streaming-Transkription während des Sprechens,
- unterbrechbare Echtzeit-Voice-Unterhaltung,
- Wake Word oder dauerhaft aktives Mikrofon,
- Klonen oder Erzeugen eigener Stimmen,
- neue Grok- oder OpenAI-Integrationsklassen,
- automatische Übersetzung in eine andere Sprache,
- Vorlesen interner Toolausgaben oder versteckter Systeminformationen,
- Umbau des Chatprotokolls auf einen einzelnen Anbieter.

Bereite den Code sauber vor, ziehe diese Funktionen aber nicht vor.

---

# 24. Entwicklungsreihenfolge

1. Chat-, Integrations-, Medien- und Agentenstrukturen analysieren.
2. genaue Ereignisse für neue und vollständig abgeschlossene Agentennachrichten bestimmen.
3. fehlende idempotente Migrationen ergänzen.
4. serverseitigen STT-Endpunkt mit Rechten, Uploadprüfung und Fähigkeitsdienst umsetzen.
5. Aufnahmeoberfläche mit Feature-Erkennung, Zuständen und Aufräumen implementieren.
6. Transkript sicher in den vorhandenen Composer übernehmen.
7. Chat-Vorleseeinstellung ergänzen und speichern.
8. Sprechtext-Normalisierung und idempotenten TTS-Auftrag umsetzen.
9. geschützte Audiodatei, Warteschlange und Autoplay-Fallback integrieren.
10. Agenten-Stimmenmaske prüfen und vervollständigen.
11. Rechte, Datenschutz, Limits, Logs und Aufräumen prüfen.
12. Anbieter-, Browser-, Fehler- und Regressionstests durchführen.

Nach jedem Schritt PHP-Syntax, JavaScript-Konsole, Browseransicht, Rechte, Datenbankzustand und Logs prüfen.

---

# 25. Abschlussbericht

Dokumentiere:

- wiederverwendete Chat- und Integrationsstrukturen,
- neue und geänderte Dateien,
- Migrationen,
- Aufnahmezustände und unterstützte Browserformate,
- serverseitigen Upload- und Transkriptionsablauf,
- Übernahme und Bestätigung des erkannten Texts,
- gespeicherte Vorleseeinstellung,
- Erkennung neuer abgeschlossener Agentennachrichten,
- Sprechtext-Normalisierung,
- Auswahl von Integration und Stimme,
- Idempotenz und Audiocache,
- Autoplay-Fallback und Wiedergabewarteschlange,
- Rechte, Limits, Datenschutz und Aufbewahrung,
- ausgeführte Tests und Ergebnisse,
- ausdrücklich zurückgestellte Funktionen.

Die Aufgabe ist abgeschlossen, wenn Benutzer im vorhandenen Chat sicher Sprache in bearbeitbaren Nachrichtentext umwandeln können, neue Agentenantworten auf Wunsch mit der korrekt zugeordneten Stimme vorgelesen werden, Browserblockaden sauber behandelt sind und weder Chat noch Oberfläche an einen konkreten Anbieter gekoppelt wurden.
