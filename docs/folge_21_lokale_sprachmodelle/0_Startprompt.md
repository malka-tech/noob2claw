# Folge 21 – Umsetzungsauftrag

Lies alle Dokumente dieses Ordners und die Grundlagen aus Folge 9, 14 bis 18
sowie Sicherheit und Mastercron aus Folge 19/20. Prüfe zuerst den tatsächlichen
Noob2Claw-Projektstand. Erhalte bestehende Änderungen und verwende die vorhandenen
Integrations-, Rechte-, Datei- und Chatdienste. Neue PHP-Fachlogik prozedural,
ohne eigene Klassen. Vorhandene Bibliotheken dürfen unverändert genutzt werden.

Implementiere einen Anbieter `lokaler_audio_server` mit den Fähigkeiten
`speech_to_text` und `text_to_speech`. Nutze den dokumentierten Videoserver-Vertrag
aus Dokument 2; keine hart codierten internen Serveradressen oder Zugangsdaten.
Keine Video-Download-/Rendering-Funktionen in dieser Folge übernehmen.

Arbeitsreihenfolge:

1. Serverinstallation und verwendeten Gateway gemäß Dokument 1 verifizieren.
   Bei unbekannten Versionen oder Hostidentität stoppen; nicht improvisieren.
2. Reproduzierbare Installationsdateien mit geprüften Versionsbindungen,
   Dienstdefinition, Konfigurationsbeispiel ohne Secrets und Rückweg erstellen.
3. Adapter in die bestehende Registry integrieren, Einstellungen und Tests bauen.
4. Geschützte Ein-/Ausgabedateien und normalisierte Fehler integrieren.
5. Explizite Umschaltung der beiden Audiofähigkeiten im Backend ermöglichen.
6. Sprachchat und Jarvis auf Kompatibilität prüfen; vorhandenes Textmodell erhalten.
7. Dokument 3 testen und Ergebnisse mit offenen Punkten dokumentieren.

Kein automatischer Cloud-Fallback, keine Produktivmigration, kein öffentlicher
Upload, kein Push. Die Ausführung dieses Prompts braucht einen tatsächlichen
Anwendungscheckout; diese Unterlagen allein implementieren keinen Adapter.
Serverinstallationen erst in einer ausdrücklich freigegebenen Testumgebung.
