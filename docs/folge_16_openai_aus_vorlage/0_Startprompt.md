# Noob2Claw – Folge 16: Startprompt
# OpenAI aus der vorhandenen Grok-Integration ableiten

Du arbeitest am bestehenden Projekt Noob2Claw.

In Folge 14 wurde die zentrale Integrationsverwaltung geschaffen. Folge 15 hat
darauf den anbieterneutralen Fähigkeitsdienst und die Grok-Integration aufgebaut.
Ergänze nun OpenAI als weiteren Anbieteradapter, ohne Grok oder die gemeinsame
Architektur zu ersetzen.

Der kurze fachliche Auftrag lautet:

```text
Nutze die vorhandene Grok-Integration als Vorlage und baue daraus eine zusätzliche OpenAI-Integration.
```

Die folgenden Leitplanken sind verbindlich.

---

# 1. Repository und Grundlagen

Repository:

```text
https://github.com/malka-tech/noob2claw.git
```

Vorlage:

```text
/home/noobclaw/noob2claw_vorlage/
```

Aktives Projekt:

```text
/mnt/noob2claw/
```

Aktualisiere die Vorlage vorsichtig und bewahre vorhandene lokale Arbeit. Lies
anschließend vollständig:

- sämtliche Dokumente unter `docs/folge_9_framework/`,
- Folge 10 für Agentenverwaltung und Agentendaten,
- Folge 14 für Registry, Integrationseinträge, Standards, Rechte und Cronjobs,
- Folge 15 für Fähigkeitsdienst, normalisierte Formate, Medienaufträge, Modelle
  und Stimmen,
- die vorhandene Grok-Implementierung im aktiven Projekt.

Vorhandene Architektur-, Datenbank-, Rechte-, Formular-, Datei-, Sicherheits-
und Logging-Standards bleiben verbindlich.

---

# 2. Aktiven Projektstand analysieren

Prüfe vor Änderungen mindestens:

```text
/mnt/noob2claw/index.php
/mnt/noob2claw/api.php
/mnt/noob2claw/inc/
/mnt/noob2claw/nav/
/mnt/noob2claw/js/
/mnt/noob2claw/css/
/mnt/noob2claw/uploads/
/mnt/noob2claw/sql/
```

Ermittle insbesondere:

- Integrationsvertrag und sichere Registry,
- zentralen Fähigkeitsdienst und Kompatibilitätsaufruf aus Folge 14,
- Struktur der Grok-Klasse und ihrer Konfiguration,
- globale Standards je Fähigkeit,
- normalisierte Anfrage- und Ergebnisformate,
- Secret-Verschlüsselung und zentralen HTTP-Client,
- Modell- und Stimmenlisten,
- Medienablage und asynchrone Aufträge,
- Rechte, CSRF-Schutz, Limits, Logging und Migrationen.

Dokumentiere kurz, welche Strukturen wiederverwendet werden. Erzeuge keine zweite
Integrations-, Rechte-, Datei-, Job- oder Einstellungsarchitektur.

---

# 3. Offizielle OpenAI-Dokumentation prüfen

Prüfe vor der Implementierung die aktuellen offiziellen OpenAI-Dokumente für:

- API-Authentifizierung,
- Textgenerierung,
- Bildgenerierung,
- Speech-to-Text,
- Text-to-Speech und Stimmen,
- Modellverfügbarkeit,
- Limits, Dateiformate und Fehlerantworten,
- Videogenerierung und Deprecations.

Modellnamen, Endpunkte, Fähigkeiten und Stimmen dürfen nicht ungeprüft aus der
Grok-Klasse übernommen werden. Die Sora Videos API ist als veraltet markiert und
für die Abschaltung am 24. September 2026 angekündigt. OpenAI darf deshalb nicht
automatisch als Standard für `video_generierung` gesetzt werden. Prüfe diesen
Stand am Tag der Umsetzung erneut.

---

# 4. Pflichtziele

1. OpenAI als zusätzliche registrierte Integrationsklasse ergänzen.
2. Grok und vorhandene Integrationseinträge unverändert funktionsfähig lassen.
3. Nur tatsächlich unterstützte Fähigkeiten deklarieren.
4. Den zentralen Fähigkeitsdienst aus Folge 15 wiederverwenden.
5. Vorhandene normalisierte Anfrage- und Ergebnisformate einhalten.
6. OpenAI-Konfiguration über die zentrale Integrationsverwaltung anbieten.
7. API-Key ausschließlich über die vorhandene Secret-Speicherung verarbeiten.
8. Modelle und Stimmen nach den vorhandenen Regeln laden und zwischenspeichern.
9. Das vorhandene Integrationstestwerkzeug ohne anbieterspezifische Sonderseite
   für OpenAI nutzbar machen.
10. Globale Standards pro Fähigkeit zwischen Grok und OpenAI umschaltbar machen.
11. Rechte, Kostenlimits, Dateigrenzen und Protokollierung wiederverwenden.
12. Idempotente, nicht destruktive Migrationen bereitstellen, falls erforderlich.

---

# 5. Adaptergrenzen und Sicherheit

- Browser und Fachmodule rufen niemals die OpenAI-Klasse direkt auf.
- Klassen und Methoden werden nur über die vorhandene Registry aufgelöst.
- Der API-Key erscheint nie in HTML, JSON-Antworten, Logs oder Fehlermeldungen.
- Leere Secret-Felder beim Bearbeiten bedeuten „unverändert“.
- Die offizielle OpenAI-Basisadresse ist fest vorgegeben beziehungsweise streng
  gegen eine Allowlist geprüft. Keine frei konfigurierbare Proxy-URL.
- Weiterleitungen, Timeouts, Antwortgrößen und erlaubte Medientypen werden
  serverseitig begrenzt.
- Anbieterfehler werden in sichere interne Fehlercodes übersetzt.
- Schreibende Aktionen prüfen Authentifizierung, Recht und CSRF-Schutz.
- Integrationseintrag, Fähigkeit und Objektzugriff werden serverseitig geprüft.

---

# 6. Kompatibilität

Der Aufruf `integration_standard_aufrufen()` aus Folge 14 bleibt funktionsfähig
und delegiert an den Fähigkeitsdienst aus Folge 15. Neuer Code verwendet
`integration_faehigkeit_aufrufen()` beziehungsweise die Variante mit bewusst
gewähltem Eintrag. Es entsteht kein paralleler OpenAI-Aufrufweg.

OpenAI bildet seine Antworten auf dieselben normalisierten Felder ab wie Grok.
Anbieterinterne IDs, Statuswerte und Fehlertexte dürfen nicht ungeprüft in die
gemeinsame Oberfläche oder das Datenmodell gelangen.

---

# 7. Tests

Prüfe mindestens:

- OpenAI-Eintrag anlegen, bearbeiten, deaktivieren und testen,
- ungültigen oder fehlenden API-Key,
- Auswahl eines nicht unterstützten Modells oder einer Fähigkeit,
- Text-, Bild-, Speech-to-Text- und Text-to-Speech-Test,
- Stimmen- und Modellaktualisierung,
- synchrone und asynchrone Ergebnisse nach vorhandenem Vertrag,
- Größen-, Laufzeit-, Kosten- und Berechtigungsgrenzen,
- maskierte Logs und sichere Fehlerausgabe,
- Umschalten der globalen Standards zwischen Grok und OpenAI,
- unveränderte Grok-Funktion nach Ergänzung des OpenAI-Adapters,
- keine angebotene OpenAI-Videofähigkeit, wenn sie laut aktueller Dokumentation
  nicht verlässlich verfügbar ist.

Führe vorhandene Tests und Syntaxprüfungen aus. Ergänze gezielte Regressionstests,
ohne externe kostenpflichtige Aufrufe ungefragt auszuführen.

---

# 8. Abschlussbericht

Berichte abschließend:

- analysierte und wiederverwendete Strukturen,
- geänderte Dateien und Migrationen,
- deklarierte OpenAI-Fähigkeiten,
- verwendete offizielle Dokumentationsstände,
- ausgeführte Tests und Ergebnisse,
- bewusst nicht unterstützte Fähigkeiten,
- verbleibende Risiken oder manuelle Schritte.
