# Noob2Claw – Folge 20: Kanban-Board und Agenten-Trigger

In Folge 20 erhält Noob2Claw einen eigenen Bereich `Kanban`. Benutzer verwalten
Boards, Spalten und Karten, ordnen Aufgaben Benutzern oder Agenten zu und können
Agenten in einem konfigurierbaren Intervall an fällige Board-Arbeit erinnern.

Der zentrale Cron-Einstieg aus Folge 14 wird zum allgemeinen Mastercronjob
erweitert. Es bleibt bei genau einem Betriebssystem-Cronjob pro Installation.

# Ergebnis

- eigener automatisch registrierter Navigationsbereich `Kanban`
- mehrere Boards mit frei sortierbaren Spalten und Karten
- Zuständigkeit für Benutzer und Agenten
- Kommentare, Checklisten, Fristen, Prioritäten und Aktivitätsverlauf
- sicherer Drag-and-drop mit serverseitiger Autorisierung
- konfigurierbarer Agenten-Trigger alle X Minuten
- idempotente Zustellung ohne Erinnerungsschleifen
- allgemeiner Masterdispatcher für Integrationen und Kanban
- ein einziger minütlicher System-Cronjob
- Rechte, Audit, Benachrichtigungen, Tests und Fehlerbehandlung

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
- `1_Kanban_und_Agenten_Trigger.md` – Fachkonzept, Datenmodell, Oberfläche,
  Agentenzustellung, Sicherheit und Tests
- `2_Mastercronjob_Einrichtung.md` – Umstellung vom Integrations-Cronjob auf den
  allgemeinen Mastercronjob

# Cron-Entscheidung

Der vorhandene Einstieg `/var/www/noobclaw/cron.php` ist die richtige technische
Grundlage. Der bisher eingerichtete Aufruf
`cron.php integrationen` reicht jedoch fachlich nicht aus, weil er ausschließlich
den Integrations-Dispatcher startet. Folge 20 ergänzt daher den erlaubten
Aufgabenschlüssel `alle` und nutzt künftig:

```cron
* * * * * /usr/bin/php /var/www/noobclaw/cron.php alle
```

`alle` ruft registrierte interne Dispatcher wie `integrationen` und
`kanban_erinnerungen` nacheinander mit Fehlerisolierung, Sperren und Laufprotokoll
auf. Es wird kein zweiter Cron-Einstieg und kein paralleler Scheduler gebaut.

# Noob2Claw-Webseite

Weitere Informationen und Folgen: https://noob2claw.de/
