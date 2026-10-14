# Folge 21 – Umstellung und Abnahme

## Sichere Reihenfolge

Bestehende Audiozuweisungen sichern, neuen Eintrag zunächst ohne Standardrolle
anlegen und die einzelnen Tests ausführen. Anschließend nur STT/TTS auf den
lokalen Anbieter umstellen. Agentenspezifische Overrides separat kontrollieren.
Grok/OpenAI für Text nicht versehentlich deaktivieren. Rückweg ist eine bewusste
Wiederherstellung der alten Audiozuweisungen durch den Administrator, niemals
ein automatischer Cloud-Fallback.

## Prüfliste

- [ ] Referenzserver identifiziert, Installationsdateien und Versionen verifiziert.
- [ ] Neuinstallation in freigegebener Testumgebung vollständig reproduziert.
- [ ] Gateway über geschützte Verbindung nur für berechtigte Clients erreichbar.
- [ ] Falsches/fehlendes Token wird abgewiesen; Geheimnisse erscheinen nirgends.
- [ ] Modellliste enthält die tatsächlich nutzbaren STT-/TTS-Kennungen.
- [ ] Kurze deutsche Testaufnahme wird nachvollziehbar transkribiert.
- [ ] Stille/leere/zu große/falsche Datei führt zu kontrolliertem Fehler.
- [ ] TTS-Test mit Umlauten, Zahlen und Satzzeichen liefert abspielbares Audio.
- [ ] JSON-Fehlerseite oder defekte Audiodaten werden nicht als Audio gespeichert.
- [ ] Berechtigungen verhindern Abruf fremder Sprachdateien.
- [ ] Transkript bleibt bearbeitbar; Senden erzeugt nur eine Chatnachricht.
- [ ] Agentenchat und Jarvis nutzen die gewählte lokale Audiointegration.
- [ ] Serverausfall zeigt einen Fehler, ohne Audio an Cloudanbieter zu senden.
- [ ] Zwei parallele Anfragen überschreiben keine Dateien und überlasten nicht
  unkontrolliert den Server; Queue oder Begrenzung funktioniert.
- [ ] Dienste starten nach Neustart; temporäre Dateien werden fristgerecht gelöscht.
- [ ] Vorher/Nachher-Zuordnung und manueller Rückweg dokumentiert.

Tests bisher nicht ausgeführt. Ein späterer Prüfbericht nennt Commit, Versionen,
Testfälle, Ergebnis, Laufzeit, Last und verbleibende Grenzen. Keine Häkchen allein
aufgrund einer geschriebenen Anleitung setzen. Für den Nachweis „kein Cloud-Audio“
auch serverseitige Verbindungsmetadaten prüfen, nicht nur den UI-Anbieternamen.
Ersteinrichtung kann Internetzugriff zum Modelldownload benötigen; Inferenz ohne
externen Dienst muss anschließend separat getestet werden.
