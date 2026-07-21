# Noob2Claw – Folge 10: Agentenverwaltung und Client entwickeln

Du arbeitest am bestehenden Projekt **Noob2Claw**.

Deine Aufgabe besteht darin, die aktuelle Projektdokumentation von GitHub zu laden und anschließend die Vorgaben aus der Datei:

```text
1_Agentenverwaltung_und_Client.md
```

vollständig umzusetzen.

---

# 1. GitHub-Repository aktualisieren

Das Repository befindet sich unter:

```text
https://github.com/malka-tech/noob2claw.git
```

Der lokale Zielpfad lautet:

```text
/home/noobclaw/noob2claw_vorlage/
```

Prüfe zuerst, ob das Repository bereits vorhanden ist.

Wenn es noch nicht vorhanden ist, führe aus:

```bash
git clone https://github.com/malka-tech/noob2claw.git \
/home/noobclaw/noob2claw_vorlage/
```

Wenn das Repository bereits vorhanden ist, aktualisiere es:

```bash
git -C /home/noobclaw/noob2claw_vorlage/ fetch origin
git -C /home/noobclaw/noob2claw_vorlage/ reset --hard origin/main
```

Prüfe anschließend den aktuellen Git-Stand:

```bash
git -C /home/noobclaw/noob2claw_vorlage/ status
git -C /home/noobclaw/noob2claw_vorlage/ log -1 --oneline
```

---

# 2. Entwicklungsgrundlagen einlesen

Lies vor Beginn der Entwicklung sämtliche Markdown-Dateien aus folgendem Verzeichnis:

```text
/home/noobclaw/noob2claw_vorlage/docs/folge_9_framework/
```

Diese Dokumente enthalten die verbindlichen Vorgaben für:

- Softwarearchitektur
- Verzeichnisstruktur
- Datenbankstruktur
- PHP-Coding-Rules
- Sicherheit
- Benutzer- und Rechteverwaltung
- Funktionen und Module
- Navigation
- Formulare
- Tabellen
- CSS
- Design
- REST-API
- MCP
- Einstellungen
- Fehlerbehandlung
- Logging

Die vorhandenen Vorgaben sind verbindlich.

Bestehende Strukturen dürfen nicht durch eigene, abweichende Konzepte ersetzt werden.

---

# 3. Aufgabenbeschreibung für Folge 10 finden

Suche im gesamten Repository nach der Datei:

```text
1_Agentenverwaltung_und_Client.md
```

Verwende dazu beispielsweise:

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '1_Agentenverwaltung_und_Client.md'
```

Falls die Datei mit einer führenden Null gespeichert wurde, suche zusätzlich nach:

```bash
find /home/noobclaw/noob2claw_vorlage/ \
-type f \
-name '*Agentenverwaltung_und_Client.md'
```

Lies die gefundene Datei vollständig ein.

Diese Datei ist die primäre Aufgabenbeschreibung für Folge 10.

---

# 4. Bestehendes Noob2Claw-System analysieren

Das aktive Webprojekt ist über SSHFS eingebunden und befindet sich unter:

```text
/mnt/noob2claw/
```

Untersuche vor Änderungen vollständig den bestehenden Projektstand.

Prüfe insbesondere:

```text
/mnt/noob2claw/index.php
/mnt/noob2claw/api.php
/mnt/noob2claw/mcp.php
/mnt/noob2claw/inc/
/mnt/noob2claw/nav/
/mnt/noob2claw/css/
/mnt/noob2claw/js/
```

Suche außerdem nach vorhandenen Dateien und Funktionen für:

- Agenten
- Benutzer
- Benutzergruppen
- Rechte
- Einstellungen
- API-Zugänge
- Sessions
- Logging
- Navigation
- Formulare
- Tabellen
- Datenbankzugriffe
- Fehlerbehandlung

Verwende bestehende Funktionen und Strukturen, statt parallele Systeme aufzubauen.

---

# 5. Aufgabe vollständig umsetzen

Setze sämtliche Anforderungen aus:

```text
1_Agentenverwaltung_und_Client.md
```

vollständig um.

Dazu gehören insbesondere:

- Agentenverwaltung im Webinterface
- benötigte Datenbanktabellen und Datenbankfelder
- Anlegen von Agenten
- Bearbeiten von Agenten
- Aktivieren und Deaktivieren von Agenten
- Löschen oder Archivieren von Agenten
- Zuweisung des Agententyps
- Zuordnung eines Modells
- Zuordnung zum ausführenden Client
- API-Zugang der Clients
- Registrierung und Authentifizierung der Clients
- Übermittlung des Clientstatus
- Übermittlung von Hardwareinformationen
- Abholung von Aufträgen
- Sperren laufender Aufträge
- Verarbeitung der Aufträge
- Übermittlung von Ergebnissen
- Fehlerprotokollierung
- Status- und Heartbeat-Verarbeitung
- Update-Verteilung des Clients
- Bereitstellung des Clientcodes auf dem Core-System
- Installation des Clients unter:

```text
/home/noobclaw/noob2claw_client/
```

---

# 6. Abgrenzung zum späteren Chatsystem

In dieser Folge wird noch kein vollständiges Chatmodul entwickelt.

Nicht Bestandteil dieser Aufgabe sind:

- mehrere Chatunterhaltungen
- vollständige Chathistorien
- Chat-Titel
- umfangreiche Nachrichtenverwaltung
- Datei- und Anhangsverwaltung für Chats
- Regenerieren von Antworten
- vollständige WebSocket-Chatoberfläche
- umfangreiche Session-Verwaltung im Browser

Zulässig ist ausschließlich eine einfache technische Testmöglichkeit, mit der geprüft werden kann:

```text
Webinterface
→ Core-API
→ Auftrag
→ Agenten-Client
→ KI-System
→ Ergebnis
→ Core-System
```

Der Fokus dieser Folge liegt auf:

```text
Agentenverwaltung
Client
API
Auftragsverteilung
Prozesssteuerung
```

---

# 7. Anforderungen an den Clientprozess

Der Client liegt auf dem Agentensystem unter:

```text
/home/noobclaw/noob2claw_client/
```

Der Hauptprozess wird über die Kommandozeile mit PHP gestartet.

Verwende dafür bevorzugt die PHP-CLI:

```bash
/usr/bin/php /home/noobclaw/noob2claw_client/client.php
```

Verwende nicht `php-cgi`, sofern kein technisch zwingender Grund vorliegt.

`php-cgi` ist für Webserver-CGI-Aufrufe gedacht. Für einen dauerhaft laufenden Konsolenprozess ist PHP-CLI die korrekte Variante.

Der Clientprozess muss folgende Logik erfüllen:

1. Der Prozess startet.
2. Der Prozess registriert sich beim Core-System.
3. Der Prozess meldet seinen Status.
4. Der Prozess fragt regelmäßig nach neuen Aufträgen.
5. Es darf immer nur ein Auftrag gleichzeitig verarbeitet werden.
6. Nach spätestens drei Minuten soll der Prozess kontrolliert beendet und neu gestartet werden.
7. Ist beim Erreichen der Drei-Minuten-Grenze noch ein Auftrag aktiv, darf dieser nicht abgebrochen werden.
8. Der aktive Auftrag wird zuerst vollständig verarbeitet.
9. Das Ergebnis wird an den Core übertragen.
10. Erst danach beendet sich der Prozess.
11. Ein externer Prozessmanager startet den Client anschließend neu.

Der Client darf sich nicht selbst unkontrolliert mit `exec`, `shell_exec` oder rekursiven PHP-Aufrufen neu starten.

Der Neustart soll über `systemd` gesteuert werden.

---

# 8. Systemd-Dienst erstellen

Erstelle einen systemd-Dienst für den Client.

Vorgesehener Pfad:

```text
/etc/systemd/system/noob2claw-client.service
```

Der Dienst soll mindestens folgende Eigenschaften besitzen:

- Start nach Netzwerkverfügbarkeit
- Ausführung als Benutzer `noobclaw`
- Arbeitsverzeichnis:

```text
/home/noobclaw/noob2claw_client/
```

- Startkommando:

```bash
/usr/bin/php /home/noobclaw/noob2claw_client/client.php
```

- automatischer Neustart nach kontrollierter Beendigung
- Neustart auch nach einem Fehler
- kurze Wartezeit zwischen zwei Starts
- automatischer Start nach einem Serverneustart
- Logging über das systemd-Journal

Beispielhafte Logik:

```ini
[Unit]
Description=Noob2Claw Agent Client
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=noobclaw
Group=noobclaw
WorkingDirectory=/home/noobclaw/noob2claw_client
ExecStart=/usr/bin/php /home/noobclaw/noob2claw_client/client.php
Restart=always
RestartSec=3
TimeoutStopSec=3600
KillSignal=SIGTERM

[Install]
WantedBy=multi-user.target
```

Prüfe vor der finalen Verwendung, ob Benutzer, Gruppe und PHP-Pfad tatsächlich vorhanden sind.

---

# 9. Kontrollierter Neustart nach drei Minuten

Die Drei-Minuten-Grenze muss innerhalb des Clients kontrolliert verarbeitet werden.

Grundprinzip:

```php
$prozess_start = time();
$neustart_nach = 180;
$auftrag_aktiv = false;

while (true) {
    if ($auftrag_aktiv === false) {
        if ((time() - $prozess_start) >= $neustart_nach) {
            break;
        }

        // Neuen Auftrag beim Core anfragen
    }

    if ($auftrag_aktiv === true) {
        // Auftrag vollständig verarbeiten
        // Ergebnis an den Core übertragen
        $auftrag_aktiv = false;

        if ((time() - $prozess_start) >= $neustart_nach) {
            break;
        }
    }

    usleep(500000);
}
```

Die konkrete Implementierung muss robust sein und zusätzlich berücksichtigen:

- API-Timeouts
- Netzwerkfehler
- fehlerhafte JSON-Antworten
- doppelte Aufträge
- bereits gesperrte Aufträge
- unerwartete Exceptions
- kontrolliertes Beenden
- Fehlerübertragung an den Core
- lokale Logausgaben
- keine Endlosschleife ohne Pause

---

# 10. Clientdateien auf dem Core bereitstellen

Der vollständige Client muss zusätzlich auf dem Core-System gespeichert werden.

Nutze dafür einen klar definierten Verzeichnisbereich innerhalb des Webprojekts, beispielsweise:

```text
/mnt/noob2claw/download/noob2claw_client/
```

oder den in der Aufgabenbeschreibung festgelegten Pfad.

Dort müssen alle für den Client erforderlichen Dateien bereitliegen, zum Beispiel:

```text
client.php
config.example.php
funktionen.php
install.sh
update.sh
noob2claw-client.service
README.md
VERSION
```

Sensible Daten dürfen dort nicht enthalten sein.

Insbesondere dürfen nicht veröffentlicht werden:

- echte API-Tokens
- Passwörter
- private Schlüssel
- produktive Serverzugänge
- lokale Zugangsdaten
- Datenbankpasswörter

---

# 11. Updatefunktion des Clients

Der Client soll seine installierte Version mit der auf dem Core verfügbaren Version vergleichen können.

Das Update muss mindestens folgende Anforderungen erfüllen:

- Versionsnummer über eine separate Datei oder API
- Update nur bei neuerer Version
- Download der neuen Dateien vom Core
- Prüfung, ob der Download vollständig war
- Schreiben in temporäre Dateien
- Backup der bisherigen Dateien
- atomarer oder möglichst sicherer Austausch
- kein Überschreiben der lokalen Konfiguration
- kein Überschreiben lokaler Tokens
- kein Update während eines aktiven Auftrags
- kontrolliertes Beenden nach erfolgreichem Update
- Neustart anschließend über systemd
- Fehlerprotokollierung bei fehlgeschlagenem Update

Die lokale Konfiguration soll getrennt vom eigentlichen Programmcode gespeichert werden.

Beispielsweise:

```text
config.php
```

Die öffentlich bereitgestellte Vorlage lautet:

```text
config.example.php
```

`config.php` darf bei Updates niemals überschrieben werden.

---

# 12. Sicherheit

Beachte zwingend:

- keine API-Tokens in URLs
- Tokenübertragung über HTTP-Header
- ausschließlich HTTPS für produktive Verbindungen
- serverseitige Prüfung aller Clientberechtigungen
- keine direkte Ausführung beliebiger Shell-Befehle aus ungeprüften API-Daten
- Shellargumente immer mit `escapeshellarg()` absichern
- keine SQL-Abfragen mit ungesicherten Benutzereingaben
- bestehende Datenbankfunktionen verwenden
- keine Zugangsdaten im Log ausgeben
- keine vollständigen Tokens im Webinterface anzeigen
- Tokens nur gehasht oder verschlüsselt speichern
- Client-ID und Client-Token getrennt behandeln
- Replay- und Doppelverarbeitung möglichst verhindern
- Aufträge vor Verarbeitung atomar sperren
- Rückgaben immer einem konkreten Auftrag zuordnen

Ein Agenten-Client darf ausschließlich Aufträge erhalten, für die er autorisiert ist.

---

# 13. Datenbankänderungen

Prüfe zuerst das vorhandene Datenbankschema.

Vorhandene Tabellen und Felder dürfen nicht unnötig dupliziert werden.

Für neue Tabellen gelten weiterhin die bestehenden Noob2Claw-Regeln:

- keine unnötigen `NULL`-Werte
- stattdessen geeignete Standardwerte wie `0` oder Leerstring
- Primärschlüssel `ID`
- sinnvolle Indizes
- Zeitstempel einheitlich behandeln
- Fremdbeziehungen logisch dokumentieren
- Tabellenpräfixe und Benennung an das Projekt anpassen
- keine direkten Datenbankzugriffe aus Navigationsdateien
- Datenbanklogik in zentrale Funktionsdateien auslagern

Erstelle notwendige Migrationen so, dass sie mehrfach ausgeführt werden können, ohne bestehende Daten zu zerstören.

---

# 14. Bestehende Benutzer- und Rechteverwaltung verwenden

Die Agentenverwaltung muss in das bestehende Rechtesystem integriert werden.

Prüfe und implementiere passende Rechte, beispielsweise:

```text
agenten_anzeigen
agenten_anlegen
agenten_bearbeiten
agenten_loeschen
agenten_client_verwalten
agenten_status_anzeigen
agenten_auftraege_anzeigen
```

Administratoren dürfen alle Funktionen verwenden.

Normale Benutzer dürfen ausschließlich die ihnen zugewiesenen Bereiche sehen.

Navigationselemente dürfen nur angezeigt werden, wenn der Benutzer das entsprechende Recht besitzt.

Auch die serverseitige Verarbeitung muss das Recht erneut prüfen.

Eine reine Ausblendung im Menü reicht nicht aus.

---

# 15. Entwicklungsregeln

Arbeite direkt am bestehenden Projekt unter:

```text
/mnt/noob2claw/
```

Beachte dabei:

- keine komplette Neuerstellung des Projekts
- keine unnötigen Frameworks
- keine Composer-Abhängigkeiten ohne zwingenden Grund
- kein Laravel
- kein Symfony
- kein React
- kein Vue
- keine parallele Datenbankabstraktion
- keine zweite Benutzerverwaltung
- keine zweite Navigation
- keine zweite API-Struktur
- keine Inline-CSS-Blöcke, wenn zentrale Styles vorgesehen sind
- keine unkontrollierten Shell-Befehle
- keine ungesicherten Dateidownloads
- keine Platzhalterimplementierung
- keine Dummy-Funktionen als angeblich fertiges Ergebnis

Bestehende Coding- und Designstandards sind einzuhalten.

---

# 16. Prüfung und Tests

Führe nach der Implementierung mindestens folgende Prüfungen durch:

## PHP-Syntax

```bash
find /mnt/noob2claw \
-type f \
-name '*.php' \
-print0 | xargs -0 -n1 php -l
```

Prüfe außerdem den Client:

```bash
find /home/noobclaw/noob2claw_client \
-type f \
-name '*.php' \
-print0 | xargs -0 -n1 php -l
```

## Berechtigungen

Prüfe:

```bash
ls -la /mnt/noob2claw/
ls -la /home/noobclaw/noob2claw_client/
```

## Systemd

Prüfe:

```bash
sudo systemd-analyze verify \
/etc/systemd/system/noob2claw-client.service
```

Nach der Installation:

```bash
sudo systemctl daemon-reload
sudo systemctl enable noob2claw-client.service
sudo systemctl restart noob2claw-client.service
sudo systemctl status noob2claw-client.service
```

Logs prüfen:

```bash
journalctl -u noob2claw-client.service -n 100 --no-pager
```

## Funktionstest

Teste mindestens folgende Abläufe:

1. Agent anlegen
2. Agent bearbeiten
3. Agent deaktivieren
4. Client registrieren
5. Client authentifizieren
6. Clientstatus übertragen
7. Hardwareinformationen übertragen
8. Auftrag anlegen
9. Auftrag durch Client abholen
10. Auftrag atomar sperren
11. Auftrag verarbeiten
12. Ergebnis übertragen
13. Auftrag abschließen
14. fehlerhaften Auftrag protokollieren
15. Client nach drei Minuten neu starten
16. aktiven Auftrag vor dem Neustart fertigstellen
17. Clientupdate erkennen
18. Clientupdate herunterladen
19. lokale Konfiguration beim Update erhalten

---

# 17. Dokumentation erstellen

Dokumentiere die implementierte Lösung.

Erstelle oder aktualisiere mindestens:

```text
/mnt/noob2claw/README.md
```

und die Clientdokumentation:

```text
/home/noobclaw/noob2claw_client/README.md
```

Die Dokumentation muss enthalten:

- Architektur
- Installationspfade
- Konfiguration
- API-Verbindung
- Registrierung
- systemd-Einrichtung
- Start und Stop
- Logs
- Updateprozess
- Sicherheit
- Fehlerbehebung
- Testablauf

---

# 18. Abschlussbericht

Beende die Aufgabe erst, wenn die vollständige Umsetzung erfolgt und geprüft wurde.

Gib anschließend einen strukturierten Abschlussbericht aus mit:

1. umgesetzten Funktionen
2. neu erstellten Dateien
3. geänderten Dateien
4. neuen Datenbanktabellen
5. geänderten Datenbanktabellen
6. neuen API-Aktionen
7. Sicherheitsmaßnahmen
8. systemd-Konfiguration
9. Client-Installationsanleitung
10. durchgeführten Tests
11. gefundenen Problemen
12. noch offenen Punkten

Behaupte keine erfolgreiche Umsetzung, wenn Tests fehlgeschlagen sind.

Bei einem Fehler:

- Fehlerursache ermitteln
- Fehler beheben
- Test erneut ausführen
- Ergebnis dokumentieren

Beginne jetzt mit dem Aktualisieren des GitHub-Repositories und arbeite die Aufgabe danach vollständig ab.
