# Folge 21 – Eigenen Audioserver installieren

## Prüfstand und Freigabegrenze

Diese Anleitung ist ein vorbereiteter Ablauf, noch keine verifizierte
Schritt-für-Schritt-Neuinstallation des Referenzservers. Die Serveridentität
muss vor dem SSH-Zugriff unabhängig bestätigt werden. Einen abweichenden
Hostschlüssel nicht durch Abschalten der Hostprüfung umgehen.

Bestätigt ist der PHP-Clientvertrag des Videoservers. Im verfügbaren
Videoserver-API-Quellstand ist `speech` mit Coqui TTS und `whisper` mit einem
lokalen Whisper-Kommando verknüpft. Ob dieser Quellstand auf dem vorgesehenen
Server läuft, ist nicht nachgewiesen. Ebenso wenig sind Paketversionen,
Modellrevisionen, Dienstnamen, GPU und Speicherbedarf nachgewiesen.
Die separate CosyVoice-Anbindung ist kein Installationsnachweis dafür.

## 1. Bestandsaufnahme – nur lesen

Nach bestätigter Serveridentität als berechtigter Administrator ausführen:

```sh
cat /etc/os-release
uname -m
command -v python3
python3 --version
command -v ffmpeg
ffmpeg -version
command -v nvidia-smi
nvidia-smi
free -h
df -h / /opt
systemctl list-units --type=service --state=running --no-pager
```

Fehlt `nvidia-smi`, ist damit GPU-Inferenz nicht nachgewiesen. Nicht blind Treiber
nachinstallieren. Für den gefundenen Dienst lokal Unit und Startskript prüfen,
aber keine Environment-Werte oder Tokens veröffentlichen. Bei Containern nur
Image-Referenz/Digest erfassen, kein ungefiltertes `docker inspect` weitergeben.

Folgende Fakten in die endgültige Anleitung übernehmen: OS-Version, Architektur,
GPU/VRAM, Treiber/CUDA, Python, isolierte Laufzeit, Paket-Lockdatei, Repository-
Commit, Modellname und Revision, Modell-Lizenz, Startskript, Dienstkonto,
Ports, Speicherverzeichnisse und gemessener Spitzenbedarf bei STT plus TTS.

## 2. Reproduzierbare Installation vorbereiten

Nach der Bestandsaufnahme müssen folgende Schritte durch konkrete, getestete
Befehle und Dateien ersetzt werden; bis dahin keine Installationsfreigabe:

1. Passende Betriebssystembasis und GPU-Laufzeit gemäß geprüftem Bestand wählen.
2. Eigenes unprivilegiertes Dienstkonto und getrennte Verzeichnisse für Code,
   Modelle, temporäre Audios und Konfiguration anlegen.
3. Den tatsächlich verwendeten Gateway auf einen Commit oder Image-Digest pinnen.
   STT/TTS-Pakete in kompatiblen isolierten Umgebungen installieren.
4. Modelle und gegebenenfalls lizenzierte Stimmen einmalig herunterladen,
   Revisionen und Prüfsummen dokumentieren. Keine privaten Sprachproben verteilen.
5. Authentifizierung, Größenlimits und zeitlich begrenzte Verarbeitung aktivieren.
   Token ausschließlich als geschützte serverseitige Konfiguration hinterlegen.
6. Dienstdefinition mit festem Arbeitsverzeichnis, begrenzter Parallelität,
   automatischem Neustart bei Fehlern und begrenzter Logaufbewahrung installieren.
7. HTTPS oder einen geschützten Tunnel verwenden. Zugriff auf die tatsächlichen
   Noob2Claw-Anwendungsserver begrenzen; Gateway nicht frei ins Internet stellen.
8. Erst lokale Modelltests, dann die HTTP-Vertragstests aus Dokument 3 ausführen.
9. Neustart testen, Aufräumregeln für temporäre Audios und Modell-Backup festlegen.

Ein Modellpaket alleine stellt noch nicht die in Dokument 2 erwarteten HTTP-
Endpunkte bereit. Ein Gateway ist erforderlich. Keine fremden API-Pfade erfinden.
Ein API-Token über ungeschütztes HTTP schützt nicht vor Mitlesen im Netzwerk.

## 3. Quellen zur Abgrenzung

[CosyVoice](https://github.com/QwenAudio/CosyVoice) und
[faster-whisper](https://github.com/SYSTRAN/faster-whisper) sind mögliche
Bausteine des separaten Speechserver-Profils. Ihre Upstream-Anleitungen ersetzen
weder die Bestandsprüfung noch das Gateway des hier vorgesehenen Videoserver-
Profils. Deshalb enthält dieser Entwurf bewusst keinen vermeintlich universellen
`pip install`-Befehl und keine unbestätigte Hardware-Mindestangabe.
