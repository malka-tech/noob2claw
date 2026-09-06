# Noob2Claw – Folge 20: Allgemeinen Mastercronjob einrichten

Diese Anleitung ersetzt den bisherigen ausschließlich auf Integrationen
begrenzten Cron-Aufruf durch einen allgemeinen Masterlauf. Sie wird erst
ausgeführt, wenn `cron.php alle`, `integrationen` und `kanban_erinnerungen`
implementiert und manuell getestet wurden.

# 1. Warum die bisherige Zeile nicht genügt

Der vorhandene System-Cronjob lautet aus Folge 14:

```cron
* * * * * /usr/bin/php /var/www/noobclaw/cron.php integrationen
```

Der Einstieg `cron.php` ist wiederverwendbar, der Aufgabenparameter
`integrationen` startet aber nur den Integrations-Dispatcher. Kanban-Erinnerungen
würden dadurch nie automatisch fällig verarbeitet.

Es wird kein zweiter Cronjob ergänzt. Stattdessen ersetzt eine allgemeine Zeile
die alte Integrationszeile:

```cron
* * * * * /usr/bin/php /var/www/noobclaw/cron.php alle
```

# 2. Voraussetzungen prüfen

Als Benutzer `noobclaw` ausführen:

```bash
whoami
command -v php
php -v
systemctl is-active cron
test -r /var/www/noobclaw/cron.php && echo 'OK: cron.php lesbar' || echo 'FEHLER: cron.php fehlt oder ist nicht lesbar'
```

Die Anleitung benötigt kein `sudo`, kein `flock`, keinen `/public/`-Unterordner
und keine separate Cron-Ausgabelogdatei. Parallelität wird in der Anwendung
atomar abgesichert. Ist der Cron-Dienst nicht aktiv, muss ein Administrator ihn
aktivieren.

# 3. Dispatcher einzeln testen

```bash
/usr/bin/php /var/www/noobclaw/cron.php integrationen
echo "Integrationen Exit-Code: $?"

/usr/bin/php /var/www/noobclaw/cron.php kanban_erinnerungen
echo "Kanban Exit-Code: $?"
```

Beide Aufrufe müssen technisch kontrolliert enden. Ein Lauf ohne fällige Arbeit
ist erfolgreich, wenn er dies mit Exit-Code `0` und plausiblen Zählerständen im
Anwendungslaufprotokoll dokumentiert.

# 4. Masterlauf testen

```bash
/usr/bin/php /var/www/noobclaw/cron.php alle
echo "Master Exit-Code: $?"
```

In Noob2Claw prüfen:

- neuer Masterlauf mit Start, Ende, Dauer und Exit-Code,
- je ein nachvollziehbarer Teillauf für Integrationen und Kanban,
- Zähler für geprüft, ausgeführt, übersprungen und fehlgeschlagen,
- keine doppelten Integrationsläufe oder Erinnerungen,
- sichere Fehler ohne Secrets.

Ein kontrollierter fachlicher Fehler eines Teildispatchers darf den anderen
Teildispatcher nicht verhindern. Bootstrap-, Registry- oder Masterfehler führen
zu einem Exit-Code ungleich `0`.

# 5. Vorhandene Crontab sichern und bearbeiten

Vorher anzeigen und die bestehende Ausgabe für den Abschlussbericht sichern:

```bash
crontab -l
crontab -e
```

Die bisherige aktive Zeile mit `cron.php integrationen` durch genau diesen Block
ersetzen:

```cron
# Noob2Claw – allgemeiner Masterdispatcher
* * * * * /usr/bin/php /var/www/noobclaw/cron.php alle
```

Nicht zusätzlich einfügen. Andere Cronjobs des Benutzers bleiben unverändert.

# 6. Gespeicherten Zustand prüfen

```bash
crontab -l
```

Erwartung:

- genau eine aktive Noob2Claw-Zeile,
- sie endet mit `cron.php alle`,
- keine aktive alte Zeile mit `cron.php integrationen`,
- absolute Pfade und keine Secrets.

Ein Neustart des Cron-Dienstes ist für eine normale Benutzer-Crontab üblicherweise
nicht nötig.

# 7. Automatischen Lauf nachweisen

Mindestens einen Minutenwechsel abwarten:

```bash
systemctl status cron --no-pager
journalctl -u cron --since '10 minutes ago' --no-pager
```

Anschließend in Noob2Claw prüfen:

1. Masterlauf wurde durch Cron nach der Änderung gestartet.
2. Integrations-Teillauf ist sichtbar.
3. Kanban-Teillauf ist sichtbar.
4. Ein fälliger Testtrigger wurde höchstens einmal zugestellt.
5. Nicht fällige Trigger wurden ohne Nachricht übersprungen.
6. Nächster Solltermin und Status sind plausibel.

# 8. Fehlerfälle

## `cron.php alle` ist unbekannt

Die Folge-20-Erweiterung des CLI-Einstiegs wurde noch nicht implementiert oder
die falsche Projektversion ist aktiv. Keinen Cron-Eintrag installieren, bis der
manuelle Masterlauf funktioniert.

## Kanban funktioniert, Integrationen nicht

Einzelaufruf `cron.php integrationen` testen, Master-Teillauf und sichere
Anwendungsfehlermeldung prüfen. Die alte Integrations-Cronzeile nicht parallel
reaktivieren.

## Erinnerungen werden doppelt gesendet

Crontab auf doppelte Einträge prüfen. Danach gemeinsame Sperre für Master- und
Einzelaufrufe, Trigger-Claim, TTL und Unique-Idempotenzschlüssel kontrollieren.
Die Intervallzeit allein verhindert keine Doppelzustellung.

## Agent erhält keine Erinnerung

Aktivstatus, Agentenzuordnung, Boardzugriff, Triggerintervall,
`naechster_lauf_am`, Backoff, Zustellstatus und vorhandenen Agentenkanal prüfen.
Keine Testnachricht an einen realen Agenten ungefragt mehrfach auslösen.

# 9. Abnahme

Der allgemeine Cronjob ist eingerichtet, wenn:

- alle drei CLI-Schlüssel kontrolliert funktionieren,
- `alle` beide Teildispatcher fehlerisoliert aufruft,
- genau eine Noob2Claw-Cronzeile aktiv ist,
- die alte Integrationszeile ersetzt wurde,
- automatischer Master-, Integrations- und Kanban-Lauf nachgewiesen sind,
- ein fälliger Agententrigger genau einmal erinnert,
- Sperren, Idempotenz, Backoff und Laufprotokolle geprüft sind,
- keine Secrets in Crontab, Nachrichten oder Protokollen erscheinen.
