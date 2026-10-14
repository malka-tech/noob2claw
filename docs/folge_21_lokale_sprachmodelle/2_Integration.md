# Folge 21 – Noob2Claw-Anbindung

## Backend-Bedienung

Unter Integrationen den Anbieter „Lokaler Audioserver“ anbieten. Der Benutzer
trägt Server-Basisadresse und API-Token ein und klickt „Verbindung testen“.
Danach separat „Transkription testen“ und „Sprachausgabe testen“. Erst nach
erfolgreichen Tests lassen sich STT und TTS ausdrücklich als Standard wählen.
Das Textmodell und dessen Anbieter bleiben unverändert.

Einstellungen: Name, aktiv, Basisadresse ohne `/v1`-Suffix, maskiertes Token,
STT-Modell `whisper`, TTS-Modell `speech`, begrenzte Timeouts und Uploadlimits.
Ein leeres Passwortfeld beim Bearbeiten überschreibt das vorhandene Token nicht.
Keine Stimmenauswahl oder Sprach-/Tempooption anzeigen, solange deren tatsächliche
Unterstützung nicht durch den Gateway nachgewiesen ist.

## Bestätigter Clientvertrag

Alle Aufrufe serverseitig mit `Authorization: Bearer <TOKEN>`.

| Aktion | Anfrage | Erwartete Antwort |
| --- | --- | --- |
| Modelle prüfen | `GET /v1/models` | JSON `data` mit Modellobjekten und `id` |
| Transkribieren | `POST /v1/audio/transcriptions`, multipart: `model=whisper`, `file` | JSON mit `text` |
| Sprache erzeugen | `POST /v1/audio/speech`, JSON `{"model":"speech","input":"Guten Tag."}` | Binäre Audiodaten mit passendem Content-Type |

Der Referenzclient kennt außerdem eine JSON-Audioantwort mit `file.content`
(Base64) und `file.filename`. Nur als explizit getesteten Kompatibilitätsfall
unterstützen; streng dekodieren, Größe begrenzen und tatsächlichen Audiotyp prüfen.
HTTP-Erfolg allein oder ein beliebiger langer Antwortkörper genügt nicht.

Kein `/v1/voices` voraussetzen: Das gehört nicht zum bestätigten Videoserver-
Vertrag. `whisper` ist eine Gateway-Kennung, kein Beleg für large-v3.
Keine globale Ersetzung durch `cosyvoice-3` oder `whisper-large-v3`.
Unbekannte Modelle ablehnen statt stillschweigend auf TTS umzuschalten.

## Anschluss an die bestehende Architektur

- Adapterfunktionen `transkribiere_audio`, `generiere_sprache`, `modelle` in den
  bestehenden Integrationsvertrag einbinden. Bestehende Fähigkeiten und zentrale
  Aufrufvermittlung verwenden, keine parallele Anbieterlogik im Chatcontroller.
- Rückgabe nach bestehendem Envelope: `erfolg`, `status`, `faehigkeit`,
  `anbieter`, `integration_eintrag_id`, `text`, `dateien`, `modell`, `fehlercode`,
  `meldung`, `wiederholbar`, `dauer_ms`, `request_id`.
- Immer den ausdrücklich gewählten Integrationseintrag verwenden. Keine Suche
  nach dem neuesten Eintrag als impliziten Ersatz und kein anderer Audioanbieter
  bei Timeout, fehlender Stimme oder deaktivierter Integration.
- Ein bereits agentenspezifisch gesetzter Cloud-Audioanbieter überschreibt einen
  globalen Standard möglicherweise: alle Agenteneinstellungen sichtbar prüfen
  und ausdrücklich umstellen. Keine verdeckte Massenänderung.
- Transkription landet zunächst als bearbeitbarer Entwurf im Chat-Eingabefeld.
  Erst „Senden“ übergibt den Text an das gewählte Chatmodell.
- Sprachdateien über geschützte lokale Datei-IDs bereitstellen, nicht über
  öffentliche Upload-URLs. Rechte beim Abruf erneut prüfen.
- API-JSON ist UTF-8; vorhandene UI-Zeichensatzgrenzen kontrolliert konvertieren.

## Schutz und Betrieb

Bestehende Login-, Objektberechtigungs- und CSRF-Prüfungen beibehalten. Nur
autorisierte Admins ändern das Ziel oder Token. Token nie an Browser, Logs oder
Fehlermeldungen ausgeben. Serverseitig MIME, Dateigröße, Audiodauer und Eigentümer
prüfen; keine vom Benutzer übergebenen lokalen Pfade öffnen.

Für interne Adressen nur das konfigurierte Ziel mit festem Host/Port zulassen,
nicht die SSRF-Prüfung global abschalten. Redirects deaktivieren, Loopback und
Metadatenziele sperren, aufgelöste Zieladressen prüfen. Zertifikate verifizieren.
Verbindungs- und Gesamtzeit begrenzen; Vorschlag 5 bzw. 300 Sekunden mit passend
konfigurierten Webserverlimits oder vorhandener asynchroner Jobverwaltung.
Bei unklarem Timeout nicht automatisch erneut generieren und Jobs verdoppeln.

Modelllisten cachen, im UI niemals bei jedem Seitenaufruf blockierend abfragen.
Ein Cacheausfall darf keine erfundenen Modelle als erfolgreich erkannt anzeigen.
Parallelität begrenzen, Überlast klar melden. Logs enthalten nur technische
Metadaten, keine Tonaufnahmen, Texte oder Tokens. Aufbewahrung und Löschung der
Audiodateien explizit regeln.

Für Audioaufrufe keinen neuen System-Cronjob anlegen. Ein dauerhaft laufender
Modelldienst ist kein Cronjob. Falls der verifizierte Gateway eine eigene Queue
benutzt, deren Worker separat dokumentieren. Noob2Claw-Aufräumjobs gehören in
den vorhandenen Mastercron aus Folge 20, nicht in doppelte Crontab-Einträge.
