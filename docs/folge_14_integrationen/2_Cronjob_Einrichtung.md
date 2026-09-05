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
Dienstbenutzer: noobclaw
Betriebsdaten:  /var/www/noobclaw/var
```

Diese Werte müssen auf dem Zielsystem geprüft und bei Abweichungen in allen
Befehlen konsistent ersetzt werden.

`cron.php` liegt direkt im Projektstamm. Der Pfad enthält bewusst keinen
Unterordner `/public/`. Die Datei ist trotzdem ausschließlich per PHP-CLI
ausführbar und lehnt HTTP-Aufrufe ab.

## Kompakter Ablauf zum Kopieren

Diese Befehle werden direkt als angemeldeter Benutzer `noobclaw` ausgeführt:

```bash
whoami
command -v php
php -v
systemctl is-active cron
ls -l /var/www/noobclaw/cron.php
test -r /var/www/noobclaw/cron.php && echo 'OK: cron.php lesbar' || echo 'FEHLER: cron.php nicht lesbar'
install -d -m 0750 /var/www/noobclaw/var
touch /var/www/noobclaw/var/noob2claw-cron.log
chmod 0640 /var/www/noobclaw/var/noob2claw-cron.log
test -w /var/www/noobclaw/var/noob2claw-cron.log && echo 'OK: Log beschreibbar' || echo 'FEHLER: Log nicht beschreibbar'
/usr/bin/php /var/www/noobclaw/cron.php integrationen
echo "Exit-Code: $?"
crontab -l
crontab -e
```

In `crontab -e` genau einmal einfügen:

```cron
# Noob2Claw – zentraler Integrations-Dispatcher
* * * * * umask 027; /usr/bin/php /var/www/noobclaw/cron.php integrationen >> /var/www/noobclaw/var/noob2claw-cron.log 2>&1
```

Danach:

```bash
crontab -l
```

Mindestens einen Minutenwechsel abwarten und anschließend prüfen:

```bash
tail -n 50 /var/www/noobclaw/var/noob2claw-cron.log
```

Bei einem Fehler abbrechen und den passenden ausführlichen Abschnitt unten
verwenden. Rechte niemals pauschal mit `chmod 777` öffnen.

---

# 2. Voraussetzungen prüfen

```bash
command -v php
php -v
systemctl is-active cron
ls -l /var/www/noobclaw/cron.php
```

Falls `systemctl is-active cron` nicht `active` meldet, muss ein Administrator
den Cron-Dienst einmalig aktivieren. Die nachfolgenden Schritte selbst benötigen
keine `sudo`-Berechtigung.

`cron.php` muss ein reiner CLI-Einstieg sein. HTTP-Aufrufe müssen abgelehnt
werden. Die Datei lädt den normalen Anwendungs-Bootstrap und ruft die zentrale
Business-Logik auf; sie enthält selbst keine Integrationsfachlogik.

---

# 3. Angemeldeten Benutzer prüfen

Der Cronjob wird in der persönlichen Crontab des bereits angemeldeten Benutzers
`noobclaw` eingerichtet. Dadurch sind weder `sudo` noch ein Wechsel zu
`www-data` erforderlich.

```bash
whoami
```

Die Ausgabe muss lauten:

```text
noobclaw
```

Leserecht auf den Cron-Einstieg prüfen:

```bash
test -r /var/www/noobclaw/cron.php
echo $?
```

`0` bedeutet, dass die Datei für diesen Benutzer lesbar ist.

---

# 4. Betriebs- und Logverzeichnis vorbereiten

```bash
install -d -m 0750 /var/www/noobclaw/var
touch /var/www/noobclaw/var/noob2claw-cron.log
chmod 0640 /var/www/noobclaw/var/noob2claw-cron.log
```

Schreibrecht prüfen:

```bash
test -w /var/www/noobclaw/var
echo $?
```

In Logs dürfen niemals API-Schlüssel, Tokens, Authorization-Header oder
vollständige sensitive Anbieterantworten erscheinen.

---

# 5. Cron-Einstieg manuell testen

Der spätere Cronbefehl wird zuerst unter dem angemeldeten Benutzer ausgeführt:

```bash
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

# 6. Vorhandene Crontab prüfen

```bash
crontab -l
```

Wenn für `noobclaw` noch keine Crontab existiert, ist die Meldung
`no crontab for noobclaw` normal. Vor dem Einrichten muss ausgeschlossen werden,
dass bereits ein gleicher Noob2Claw-Eintrag vorhanden ist.

---

# 7. Cronjob produktiv einrichten

Persönliche Crontab öffnen:

```bash
crontab -e
```

Folgenden Block eintragen:

```cron
# Noob2Claw – zentraler Integrations-Dispatcher
* * * * * umask 027; /usr/bin/php /var/www/noobclaw/cron.php integrationen >> /var/www/noobclaw/var/noob2claw-cron.log 2>&1
```

Anschließend den gespeicherten Eintrag kontrollieren:

```bash
crontab -l
```

Die Cronzeile enthält ausschließlich absolute Pfade und keine Secrets. Ein
Neustart des Cron-Dienstes ist bei einer normalen Crontab-Änderung üblicherweise
nicht erforderlich. Maßgeblich ist das Verhalten des Zielsystems.

---

# 8. Automatischen Lauf nachweisen

Nach mindestens einem Minutenwechsel das Ausgabelog prüfen:

```bash
tail -n 50 /var/www/noobclaw/var/noob2claw-cron.log
```

Bei Bedarf zusätzlich:

```bash
systemctl status cron --no-pager
journalctl -u cron --since '10 minutes ago' --no-pager
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

# 9. Fehlerisolierung prüfen

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

# 10. Häufige Fehler

## `noobclaw` ist nicht in der sudoers-Datei

Für diese Anleitung wird `sudo` nicht benötigt. Der Benutzer `noobclaw`
verwaltet seine eigene Crontab mit `crontab -l` und `crontab -e`. Nicht
`sudo crontab -u www-data ...` verwenden.

Falls bereits `crontab -e` selbst verweigert wird oder der Cron-Dienst inaktiv
ist, muss ein Administrator den Dienst beziehungsweise `/etc/cron.allow` und
`/etc/cron.deny` prüfen.

## `cron.php` wurde nicht gefunden

Projektpfad und Groß-/Kleinschreibung prüfen:

```bash
ls -l /var/www/noobclaw/cron.php
```

## `Permission denied`

Benutzer sowie Lese- und Schreibrechte prüfen:

```bash
test -r /var/www/noobclaw/cron.php
test -w /var/www/noobclaw/var
```

## Manueller Lauf funktioniert, automatischer Lauf nicht

```bash
crontab -l
systemctl status cron --no-pager
journalctl -u cron --since '10 minutes ago' --no-pager
```

Besonders häufig sind ein falscher Benutzer, relative Pfade oder fehlende
Schreibrechte die Ursache.

## Lauf startet doppelt

- Crontab auf doppelte Einträge prüfen.
- globale Anwendungssperre und atomare Eintrag-Claims prüfen.
- kontrollierten Umgang mit abgelaufenen Sperren testen.

## Log bleibt leer

- absoluten PHP-Pfad prüfen,
- Logverzeichnis und Schreibrechte prüfen,
- Cron-Journal kontrollieren,
- Befehl exakt als Benutzer `noobclaw` manuell ausführen.

---

# 11. Cronlog begrenzen

Vor dem produktiven Dauerbetrieb muss außerdem eine vorhandene zentrale
anwendungsseitige Logbegrenzung auf
`/var/www/noobclaw/var/noob2claw-cron.log` angewendet werden. Falls das Projekt
noch keine zentrale Lösung besitzt, muss ein Administrator ergänzend eine
`logrotate`-Regel mit begrenzter Zahl archivierter Dateien, Komprimierung und
passenden Dateirechten einrichten. Dieser optionale Systemschritt kann durch
`noobclaw` ohne Administratorrechte nicht vorgenommen werden. Das Cronlog darf
nicht unbegrenzt wachsen. Anwendungsläufe bleiben zusätzlich strukturiert in der
Datenbank protokolliert.

---

# 12. Cronjob deaktivieren oder entfernen

Crontab öffnen:

```bash
crontab -e
```

Nur den eindeutig kommentierten Noob2Claw-Block entfernen oder vorübergehend
auskommentieren. Andere Cronjobs dieses Benutzers dürfen nicht verändert werden.

Danach kontrollieren:

```bash
crontab -l
```

Das Entfernen der Crontab-Zeile löscht keine Integrationsdaten oder Laufprotokolle.

---

# 13. Abnahme

Der Cronjob gilt erst als vollständig eingerichtet, wenn:

- der Cron-Dienst aktiv ist,
- `cron.php integrationen` als Benutzer `noobclaw` erfolgreich läuft,
- Betriebs- und Logpfad beschreibbar sind,
- parallele Läufe verhindert werden,
- die Crontab genau einen kommentierten Noob2Claw-Eintrag enthält,
- ein echter automatischer Lauf nachgewiesen wurde,
- Fälligkeit, Fehlerisolierung und nächster Lauf korrekt funktionieren,
- Oberfläche und Logs keine Secrets offenlegen,
- das Cron-Ausgabelog rotiert oder anderweitig wirksam begrenzt wird,
- der finale Benutzer, PHP-Pfad, Projektpfad, Logpfad und Crontab-Eintrag
  im Abschlussbericht dokumentiert sind.
