# Noob2Claw – Folge 22: Startprompt Webseite

Implementiere im bestehenden Noob2Claw-Projekt die öffentliche Webseitenplattform
gemäß allen Dokumenten dieses Ordners. Analysiere zuerst den realen Projektstand
und erhalte vorhandene Änderungen. Dieses Konzept ist kein Nachweis einer schon
bereitgestellten Funktion.

## Grundlagen und Bestand

Repository: `https://github.com/malka-tech/noob2claw.git`.
Vorlage: `/home/noobclaw/noob2claw_vorlage/`, aktives Projekt bisher
`/mnt/noob2claw/`; reale Pfade vor der Arbeit prüfen.

Lies die verbindlichen Grundlagen aus Folge 9 sowie die relevanten Teile der
Folgen 10 bis 20, insbesondere Kunden-/Benutzerrechte, API/MCP, Sicherheitszentrale
und Mastercron. Lies alle begleitenden Dokumente dieser Folge vollständig,
insbesondere die verbindlichen Bedienabläufe aus Dokument 4.

Prüfe Bootstrap, Sessionstart, Login-/Logout-Weiterleitungen, URL-Erzeugung,
Webserverregeln, automatische Funktionsladung und vorhandene Templates. Die
bisherige Regel „alle Seiten über index.php“ gilt weiter für das Backend;
diese Folge ergänzt ausdrücklich `webseite.php` als öffentlichen Renderer.
Alle REST-Aktionen bleiben im bestehenden `api.php`, MCP bleibt in `mcp.php`.

## Implementierungsauftrag

1. Backend-Bereich Webseiten mit Webseiten, Domains und Bereichen erstellen.
2. Inhaltsseiten, Bereichszuordnung, Navigation, Entwurf, Vorschau,
   Veröffentlichung und SEO-Metadaten implementieren.
3. Den öffentlichen Einstieg und die Route-Reservierungen aus Dokument 2 umsetzen.
4. Ein lauffähiges Referenztemplate `/templates/standard/` erstellen.
5. Registriertes Inhaltsmodul samt Beispielseite und Bereichsblöcken umsetzen.
6. Globale Kundenverwaltung getrennt von Backend-Benutzern implementieren.
7. Registrierung, Verifikation, Anmeldung, Abmeldung und Passwort-Reset erstellen.
8. Link „Backend anmelden“ in das Template aufnehmen; bestehenden Backend-Login
   einschließlich seiner Sicherheitsprüfungen wiederverwenden.
9. Modulregistry, Aktivierung je Webseite, Route- und Slotzuordnung implementieren.
10. Rechte, Schutz vor Zugriffen auf fremde Webseiten, Cachetrennung und Tests
    gemäß Dokument 3 ergänzen.
11. Drei-Schritt-Assistent mit wiederaufnehmbarem Entwurf ohne Domain erstellen.
12. Zwei Inhaltsvorlagen, Blockeditor mit Desktop-/Mobilvorschau, Medienauswahl
    und einfache Menübearbeitung gemäß Dokument 4 fertigstellen.
13. Autosave nur für Entwürfe, sichtbaren Speicherstatus, Versionskonflikte,
    Versionsverlauf und Papierkorb umsetzen.
14. Seiten-, Block-, Routen-, Medien-, Menü- und Designstände so versionieren,
    dass ausschließlich eine explizite Veröffentlichung die Liveausgabe ändert.

Die Inhaltspflege darf weder PHP-Kenntnisse noch Eingabe von Slotnamen,
Modulschlüsseln oder technischen Pfaden voraussetzen. Die Oberfläche zeigt den
aktuellen Webseitenkontext und einen verständlichen nächsten Schritt.

Die Umsetzung verwendet prozedurale Funktionen mit deutschen Namen. Neue eigene
Klassen sind nicht erforderlich; eingebundene Bibliotheken bleiben möglich.
SQL gehört in zentrale Datenfunktionen unter `inc/`; Templates rendern bereits
vorbereitete Daten. Bestehende Darstellungs-/Zeichensatzkonventionen erhalten:
ISO-Masken nicht still nach UTF-8 konvertieren; Grenzen zu UTF-8-JSON, URLs und
Datenbank ausdrücklich zentral konvertieren und Umlaute testen.
PDO und die bestehende `utf8mb4`-Datenbank bleiben erhalten; die Maskenvorgabe
ist kein Auftrag zur Konvertierung des Datenbankschemas.

## Reihenfolge und Abschluss

Zuerst additive Migrationen und Funktionen, dann Templates und Kundenabläufe,
danach Routing auf einer Testinstanz prüfen. Produktive Webserverregeln erst
nach einem getesteten Rückweg umstellen. Bestehende Integrations-/Kanbanjobs
bleiben über `cron.php alle` erreichbar. Erforderliche Tokenbereinigung als
registrierte Wartungsaufgabe an diesen Einstieg anbinden.

Dokumentiere geänderte Dateien, Migrationen, tatsächliche Routingregeln,
Template-/Modulvertrag, Rechte, Testergebnisse und verbleibende Einschränkungen.
Behaupte nur tatsächlich ausgeführte Prüfungen. Die Abnahme verlangt zwei
Webseiten, getrennte Backend-/Kundensitzungen sowie nachgewiesene Backend-,
API-, MCP- und Cron-Kompatibilität.
