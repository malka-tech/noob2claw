# Noob2Claw – Folge 14: Zentralen Cronjob einrichten

Diese Anleitung nimmt den in Folge 14 entwickelten zentralen Noob2Claw-Cronjob
auf einem Debian-System in Betrieb. Sie wird erst ausgeführt, nachdem `cron.php`
und der Integrations-Dispatcher implementiert und getestet wurden.

Der Cronjob startet den Dispatcher jede Minute. Der Dispatcher entscheidet anhand
von `naechster_lauf_am`, welche Integration tatsächlich fällig ist. Open-Meteo
wird deshalb trotz des minütlichen Starts höchstens im konfigurierten Intervall
abgerufen.

---

# 1. Annahmen und Pfade

Die Beispiele verwenden:

```text
Projekt:        /var/www/noobclaw
Cron-Einstieg:  /var/www/noobclaw/cron.php
PHP:            /usr/bin/php
Dienstbenutzer: www-data
Betriebsdaten:  /var/www/noobclaw/var
```

Diese Werte müssen auf dem Zielsystem geprüft und bei Abweichungen in allen
Befehlen konsistent ersetzt werden.

---

# 2. Voraussetzungen prüfen

```bash
command -v php
command -v flock
php -v
systemctl is-active cron
ls -l /var/www/noobclaw/cron.php
```

Falls der Cron-Dienst nicht aktiv ist:

```bash
sudo systemctl enable --now cron
systemctl status cron --no-pager
```

`cron.php` muss ein reiner CLI-Einstieg sein. HTTP-Aufrufe müssen abgelehnt
werden. Die Datei lädt den normalen Anwendungs-Bootstrap und ruft die zentrale
Business-Logik auf; sie enthält selbst keine Integrationsfachlogik.

---

# 3. Richtigen Dienstbenutzer ermitteln

Auf einer üblichen Debian-/Apache-Installation läuft die Anwendung als
`www-data`. Das muss geprüft werden:

```bash
ps -eo user,comm | grep -E 'apache2|php-fpm'
stat -c '%U:%G %n' /var/www/noobclaw
```

Leserecht auf den Cron-Einstieg prüfen:

```bash
sudo -u www-data test -r /var/www/noobclaw/cron.php
echo $?
```

`0` bedeutet, dass die Datei für diesen Benutzer lesbar ist.

---

# 4. Lock- und Logverzeichnis vorbereiten

```bash
sudo install -d \
  -o www-data \
  -g www-data \
  -m 0750 \
  /var/www/noobclaw/var
```

Schreibrecht prüfen:

```bash
sudo -u www-data test -w /var/www/noobclaw/var
echo $?
```

In Logs dürfen niemals API-Schlüssel, Tokens, Authorization-Header oder
vollständige sensitive Anbieterantworten erscheinen.

---

# 5. Cron-Einstieg manuell testen

Der spätere Cronbefehl wird zuerst unter exakt demselben Benutzer ausgeführt:

```bash
sudo -u www-data \
  /usr/bin/php \
  /var/www/noobclaw/cron.php \
  integrationen

echo $?
```

Erwartung:

- Exit-Code `0`: Der Dispatcher wurde technisch vollständig ausgeführt.
- Exit-Code ungleich `0`: Bootstrap, Datenbank oder Dispatcher konnten nicht
  sicher ausgeführt werden.
- Ein kontrollierter Fehler eines einzelnen Integrationseintrags beendet den
  Dispatcher nicht und wird im Anwendungslauf protokolliert.

Danach in Noob2Claw prüfen:

- Zeitpunkt und Status des letzten Dispatcher-Laufs,
- geprüfte, ausgeführte, übersprungene und fehlgeschlagene Einträge,
- letzten Versuch und letzten Erfolg des Open-Meteo-Eintrags,
- nächsten geplanten Lauf,
- gespeicherte Wetterdaten.

---

# 6. Betriebssystem-Sperre testen

Zusätzlich zu den atomaren Anwendungssperren verhindert `flock` parallele
Prozessstarts:

```bash
sudo -u www-data \
  /usr/bin/flock -n \
  /var/www/noobclaw/var/noob2claw-cron.lock \
  /usr/bin/php \
  /var/www/noobclaw/cron.php \
  integrationen
```

`flock` ist nur eine zusätzliche Schutzschicht. Globale Dispatcher-Sperre,
atomare Eintrag-Claims und TTLs innerhalb der Anwendung bleiben Pflicht.

---

# 7. Vorhandene Crontab prüfen

```bash
sudo crontab -u www-data -l
```

Wenn für `www-data` noch keine Crontab existiert, ist die entsprechende Meldung
normal. Vor dem Einrichten muss ausgeschlossen werden, dass bereits ein gleicher
Noob2Claw-Eintrag vorhanden ist.

---

# 8. Cronjob produktiv einrichten

Crontab des Dienstbenutzers öffnen:

```bash
sudo crontab -u www-data -e
```

Folgenden Block eintragen:

```cron
# Noob2Claw – zentraler Integrations-Dispatcher
* * * * * /usr/bin/flock -n /var/www/noobclaw/var/noob2claw-cron.lock /usr/bin/php /var/www/noobclaw/cron.php integrationen >> /var/www/noobclaw/var/noob2claw-cron.log 2>&1
```

Anschließend den gespeicherten Eintrag kontrollieren:

```bash
sudo crontab -u www-data -l
```

Die Cronzeile enthält ausschließlich absolute Pfade und keine Secrets. Ein
Neustart des Cron-Dienstes ist bei einer normalen Crontab-Änderung üblicherweise
nicht erforderlich. Maßgeblich ist das Verhalten des Zielsystems.

---

# 9. Automatischen Lauf nachweisen

Nach mindestens einem Minutenwechsel das Ausgabelog prüfen:

```bash
sudo tail -n 50 /var/www/noobclaw/var/noob2claw-cron.log
```

Bei Bedarf zusätzlich:

```bash
systemctl status cron --no-pager
sudo journalctl -u cron --since '10 minutes ago' --no-pager
```

In der Noob2Claw-Oberfläche muss ein neuer, automatisch erzeugter
Dispatcher-Lauf sichtbar sein. Ein manueller Test allein beweist nicht, dass der
Betriebssystem-Cronjob aktiv ist.

Der automatische Nachweis ist erfolgreich, wenn:

1. der Zeitstempel des Dispatcher-Laufs nach der Einrichtung liegt,
2. der Lauf technisch erfolgreich beendet wurde,
3. ein fälliger Open-Meteo-Eintrag verarbeitet wurde,
4. letzter Versuch, letzter Erfolg und nächster Lauf plausibel sind,
5. kein doppelter oder paralleler Lauf entstand.

---

# 10. Fehlerisolierung prüfen

Für einen kontrollierten Test können zwei Open-Meteo-Einträge verwendet werden:

1. einen Eintrag korrekt konfigurieren,
2. einen zweiten Eintrag vorübergehend mit einem ungültigen Standort versehen,
3. beide Einträge auf fällig setzen,
4. den Dispatcher ausführen lassen,
5. den Fehler des zweiten Eintrags im sicheren Log prüfen,
6. den erfolgreichen Lauf des ersten Eintrags nachweisen,
7. die falsche Konfiguration anschließend korrigieren.

Ein Fachfehler darf andere fällige Integrationseinträge nicht blockieren.

---

# 11. Häufige Fehler

## `cron.php` wurde nicht gefunden

Projektpfad und Groß-/Kleinschreibung prüfen:

```bash
ls -l /var/www/noobclaw/cron.php
```

## `Permission denied`

Benutzer sowie Lese- und Schreibrechte prüfen:

```bash
sudo -u www-data test -r /var/www/noobclaw/cron.php
sudo -u www-data test -w /var/www/noobclaw/var
```

## Manueller Lauf funktioniert, automatischer Lauf nicht

```bash
sudo crontab -u www-data -l
systemctl status cron --no-pager
sudo journalctl -u cron --since '10 minutes ago' --no-pager
```

Besonders häufig sind ein falscher Benutzer, relative Pfade oder fehlende
Schreibrechte die Ursache.

## Lauf startet doppelt

- Crontab auf doppelte Einträge prüfen.
- `flock`-Pfad und Berechtigungen prüfen.
- globale Anwendungssperre und atomare Eintrag-Claims prüfen.
- kontrollierten Umgang mit abgelaufenen Sperren testen.

## Log bleibt leer

- absoluten PHP-Pfad prüfen,
- Logverzeichnis und Schreibrechte prüfen,
- Cron-Journal kontrollieren,
- Befehl exakt als Dienstbenutzer manuell ausführen.

---

# 12. Cronlog begrenzen

Vor dem produktiven Dauerbetrieb muss außerdem eine vorhandene zentrale
Logrotation beziehungsweise Logbegrenzung auf
`/var/www/noobclaw/var/noob2claw-cron.log` angewendet werden. Falls das Projekt
noch keine zentrale Lösung besitzt, ist auf Debian eine `logrotate`-Regel mit
begrenzter Zahl archivierter Dateien, Komprimierung und passenden Dateirechten
einzurichten. Das Cronlog darf nicht unbegrenzt wachsen. Anwendungsläufe bleiben
zusätzlich strukturiert in der Datenbank protokolliert.

---

# 13. Cronjob deaktivieren oder entfernen

Crontab öffnen:

```bash
sudo crontab -u www-data -e
```

Nur den eindeutig kommentierten Noob2Claw-Block entfernen oder vorübergehend
auskommentieren. Andere Cronjobs dieses Benutzers dürfen nicht verändert werden.

Danach kontrollieren:

```bash
sudo crontab -u www-data -l
```

Das Entfernen der Crontab-Zeile löscht keine Integrationsdaten oder Laufprotokolle.

---

# 14. Abnahme

Der Cronjob gilt erst als vollständig eingerichtet, wenn:

- der Cron-Dienst aktiv ist,
- `cron.php integrationen` als Dienstbenutzer erfolgreich läuft,
- Lock- und Logpfad beschreibbar sind,
- parallele Läufe verhindert werden,
- die Crontab genau einen kommentierten Noob2Claw-Eintrag enthält,
- ein echter automatischer Lauf nachgewiesen wurde,
- Fälligkeit, Fehlerisolierung und nächster Lauf korrekt funktionieren,
- Oberfläche und Logs keine Secrets offenlegen,
- das Cron-Ausgabelog rotiert oder anderweitig wirksam begrenzt wird,
- der finale Benutzer, PHP-Pfad, Projektpfad, Lockpfad, Logpfad und Crontab-Eintrag
  im Abschlussbericht dokumentiert sind.
