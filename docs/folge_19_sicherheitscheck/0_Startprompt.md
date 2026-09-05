# Noob2Claw – Folge 19: Startprompt
# Vollständiger Sicherheitscheck mit Reporting und Behebung

Du arbeitest als Sicherheitsprüfer und Entwickler am bestehenden Projekt
Noob2Claw. Dieser Auftrag kann durch einen OpenClaw- oder Hermes-Agenten
ausgeführt werden.

Prüfe den vollständigen aktuellen Code auf Sicherheitslücken. Dokumentiere nur
belegbare Befunde, behebe bestätigte Probleme kontrolliert und weise durch
Regressionstests und einen erneuten Sicherheitscheck nach, dass die Ursache
beseitigt wurde.

Du darfst nicht behaupten, die Anwendung sei vollständig sicher. Zulässig ist
nur eine auf Scope, Methoden, Belegen und Restrisiken beruhende Aussage.

---

# 1. Verbindliche Grundlagen

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
vollständig:

- sämtliche Dokumente unter `docs/folge_9_framework/`,
- die relevanten Dokumente der Folgen 10 bis 18,
- `docs/folge_19_sicherheitscheck/1_Sicherheitsaudit.md`,
- `docs/folge_19_sicherheitscheck/2_Berichtsvorlage.md`.

Bestehende Architektur, zentrale Business-Funktionen, deutsche Benennung,
Rechteverwaltung, API-/MCP-Routing, Dateiverwaltung, Logging und Migrationstechnik
bleiben verbindlich. Erzeuge keine parallelen Sicherheitssysteme.

---

# 2. Grenzen und sichere Arbeitsweise

Vor jeder Prüfung:

1. aktiven Branch, Commit-ID und `git status` erfassen,
2. vorhandene Benutzeränderungen identifizieren und erhalten,
3. zunächst ausschließlich lesend arbeiten,
4. Scope und nicht erreichbare Bereiche dokumentieren,
5. aktive Tests nur gegen eine ausdrücklich festgelegte lokale oder isolierte
   Testinstanz ausführen.

Verboten:

- Tests gegen Produktion oder fremde Systeme,
- Aufrufe realer Anbieter-APIs nur zum Erzeugen von Testlast,
- DoS-, Last-, Brute-Force-, Social-Engineering- oder Persistenztests,
- Löschen oder Verändern produktiver Daten,
- Ausgabe echter Passwörter, Tokens, Sessionwerte, Schlüssel oder Personendaten,
- Aufnahme echter Secrets in Prompt, Bericht, Testfixture, Log oder Git,
- Installation neuer Scanwerkzeuge ohne Freigabe,
- ungefragte rekursive Eigentümer- oder Rechteänderungen,
- Fixes ohne reproduzierbaren Befund,
- erfundene Testergebnisse oder Erfolgsbehauptungen.

Wenn ein Test Schaden verursachen, Daten migrieren, öffentliche Verträge brechen
oder externe Systeme betreffen könnte, halte an und fordere eine Freigabe an.

---

# 3. Auditstand sichern

Dokumentiere mindestens:

```bash
git status --short --branch
git branch --show-current
git rev-parse HEAD
```

Erstelle vor Behebungen einen eigenen Branch, sofern die Umgebung dies erlaubt:

```text
security/folge-19-audit
```

Der Bericht vor der Behebung bleibt als unveränderte Auditspur erhalten. Behebe
keine Lücke, bevor ihr Befund mit stabiler ID dokumentiert wurde.

---

# 4. Vollständigen Scope erfassen

Prüfe mindestens:

- sämtliche PHP-, JavaScript-, HTML-, CSS- und SQL-Dateien,
- `index.php`, `api.php`, `mcp.php` und `cron.php`,
- `inc/`, `nav/`, `js/`, `css/`, `sql/`, Upload- und Medienlogik,
- Composer- und npm-Abhängigkeiten, sofern vorhanden,
- Konfigurations- und Beispieldateien,
- mitgelieferte Apache-, PHP-, Cron- und Deployment-Konfiguration,
- Login, Logout, Passwortänderung und Sessions,
- Benutzer, Rollen, Rechte und direkte Objektberechtigungen,
- REST-API und MCP,
- Agentenverwaltung, Agenten-Clients und Aufträge,
- Chat, Nachrichten, Uploads und geschützte Downloads,
- Wiki und MCP-Wiki-Funktionen,
- Integrationen, Anbieterzugänge und Secret-Speicherung,
- Speech-to-Text, Text-to-Speech und Mediendateien,
- zentralen Cron-CLI-Einstieg und Dispatcher,
- Jarvis-Maske und asynchrone Browserzustände,
- Logging, Fehlerausgaben, Datenschutz und Aufbewahrungsfristen,
- Datenbankschema und idempotente Migrationen,
- Git-Historie auf versehentlich eingecheckte Secrets, ohne Treffer auszugeben.

Nicht vorhandene oder nicht erreichbare Bereiche werden als `nicht prüfbar`
markiert, niemals als bestanden.

---

# 5. Bedrohungsmodell erstellen

Dokumentiere Schutzwerte, Angreifer, Vertrauensgrenzen und Missbrauchsfälle.

Schutzwerte mindestens:

- Konten, Sessions, Rollen und Rechte,
- Chats, Nachrichten, Wiki- und Agentendaten,
- Uploads, Audio, Bilder und Videos,
- API-, MCP- und Anbieter-Schlüssel,
- Datenbank, Dateisystem, Cronjob und Logs.

Vertrauensgrenzen mindestens:

```text
Browser       → index.php und api.php
API-Client    → api.php
MCP-Client    → mcp.php
System-Cron   → cron.php
Anwendung     → Datenbank und Dateisystem
Anwendung     → externe Anbieter
Benutzertext  → KI-Agent
KI-Ausgabe    → Anwendung und Browser
```

Prüfe insbesondere Rechteausweitung, IDOR/BOLA, SQL-Injection, XSS, CSRF, SSRF,
Pfadmanipulation, gefährliche Uploads, Command Injection, Secret-Leaks, Prompt
Injection mit Toolzugriff, Race Conditions und Ressourcenmissbrauch.

---

# 6. Automatische Prüfungen

Ermittle zuerst, welche Werkzeuge bereits installiert sind, und protokolliere
deren Version. Führe nur passende vorhandene Werkzeuge aus. Beispiele:

```bash
find . -type f -name '*.php' -not -path './vendor/*' -print0 \
  | xargs -0 -n1 php -l

composer audit
npm audit
semgrep scan --config auto .
gitleaks detect --redact
```

Regeln:

- Befehle nur ausführen, wenn die zugehörige Projektdatei und das Werkzeug
  vorhanden sind.
- Keine Abhängigkeiten aktualisieren, nur um einen Scan auszuführen.
- Keine Scanner ungefragt installieren.
- Passive Webscanner nur gegen die freigegebene Testinstanz richten.
- Fehlende Werkzeuge als `nicht ausgeführt` dokumentieren.
- Scannerfunde manuell verifizieren und Fehlalarme begründen.
- Geheimnisfunde immer redigieren; niemals den Fundwert ausgeben.

---

# 7. Manuelle Prüfung

Verfolge Eingaben vollständig von Quelle bis Senke. Prüfe mindestens:

## Authentifizierung und Sessions

- sichere Passwort-Hashes und kein Klartext,
- Session-Regeneration nach Anmeldung und Berechtigungswechsel,
- `HttpOnly`, angemessenes `SameSite` und `Secure` bei HTTPS,
- Timeout, Logout und Sperrung,
- Rate-Limits gegen Loginmissbrauch,
- keine Session-ID in URL, Log oder Datenbank im Klartext.

## Autorisierung

- jede Route und Aktion prüft Rechte serverseitig,
- Navigation ist niemals die Autorisierung,
- direkte URLs werden geprüft,
- fremde Benutzer-, Agenten-, Chat-, Nachrichten-, Wiki-, Datei-, Integrations-
  und Medien-IDs werden abgelehnt,
- Administratorverhalten beruht auf Rechten, nicht auf fest codierten IDs.

## Eingaben, SQL und Ausgabe

- serverseitige Typ-, Format-, Längen- und Bereichsprüfung,
- parametrisierte SQL-Abfragen,
- keine dynamischen Tabellen-, Spalten-, Klassen-, Methoden- oder Include-Namen
  aus ungeprüften Eingaben,
- kontextgerechtes HTML-, Attribut-, URL- und JavaScript-Escaping,
- CSRF-Schutz für jede schreibende Webaktion,
- keine internen Pfade, SQL-Texte oder Stacktraces in Antworten.

## Dateien und Medien

- Größen-, Endungs-, MIME- und Dateisignaturprüfung,
- servergenerierte Dateinamen und kein Benutzerpfad,
- keine ausführbaren Uploadformate oder PHP-Ausführung im Uploadbereich,
- Downloads nur über Authentifizierung und Objektberechtigung,
- sichere Content-Type-, Content-Disposition- und Nosniff-Header,
- geregelte Löschung temporärer Audio- und Mediendateien.

## API, MCP und Agenten-Clients

- zentrale Authentifizierung, Rechte, Validierung und Fehlerantworten,
- Tokens ausschließlich im vorgesehenen Header, nicht in URLs,
- MCP-Agentensuffix erzeugt keine zusätzliche Berechtigung,
- Toolrecht und fachliches Objektrecht werden beide geprüft,
- keine ungeprüfte KI-Ausgabe als SQL, Shellbefehl, Dateipfad oder Toolargument,
- Größen-, Frequenz- und Auftragslimits.

## Integrationen und SSRF

- feste beziehungsweise streng erlaubte API-Ziele,
- Blockierung lokaler, privater und Metadatenadressen,
- erneute Prüfung jedes Redirect-Ziels,
- HTTPS, Timeouts, Antwortgrößen-, Content-Type- und Schemaprüfung,
- Anbieterantworten immer als nicht vertrauenswürdig behandeln,
- Secrets verschlüsselt speichern und nur serverseitig entschlüsseln.

## Cron und Hintergrundaufträge

- `cron.php` liegt direkt im Projektstamm und lehnt HTTP-Aufrufe ab,
- feste Allowlist erlaubter CLI-Aufgaben,
- globale atomare Anwendungssperre und atomare Claims mit TTL,
- keine parallele zweite Schedulerarchitektur,
- sichere Exit-Codes, Fehlerisolierung und Logs ohne Secrets.

## Konfiguration und Datenschutz

- Debugmodus und Fehleranzeige in Produktion deaktiviert,
- Logs und interne Dateien nicht direkt per Browser erreichbar,
- Security Header und sichere Cookieeinstellungen,
- keine Zugangsdaten in Quellcode, Git-Historie oder Beispieldateien,
- Datenminimierung für Prompts, Chats, Transkripte und Medien,
- Aufbewahrungs- und Löschregeln.

---

# 8. Befunde belegen und priorisieren

Jeder bestätigte Befund erhält eine stabile ID `N2C-SEC-###` und alle Felder aus
`2_Berichtsvorlage.md`.

Verwende die Schweregrade:

```text
kritisch
hoch
mittel
niedrig
informativ
```

Ordne nach Möglichkeit eine CWE und eine versionierte OWASP-ASVS-Anforderung zu.
CVSS darf ergänzend verwendet werden, ersetzt aber nicht die Einschätzung der
realen Daten, Rechte und Reichweite.

Ein Befund gilt nur als bestätigt, wenn Codepfad, Voraussetzung, Ursache,
Auswirkung und sicherer Reproduktionsweg nachvollziehbar sind. Verwende
ausschließlich synthetische Testwerte und veröffentliche keinen produktionsreifen
Exploit.

Kritische Befunde blockieren jede Freigabe. Hohe Befunde benötigen Behebung oder
eine ausdrückliche dokumentierte Risikoentscheidung durch den Verantwortlichen.

---

# 9. Berichte vor der Behebung erstellen

Erzeuge außerhalb öffentlich ausgelieferter Verzeichnisse:

```text
sicherheitsbericht_vor_behebung.md
sicherheitsbefunde.json
```

Berichte dürfen keine Secrets, vollständigen Sessionwerte, personenbezogenen
Echtdaten oder direkt missbrauchbaren produktiven Exploits enthalten.

Die JSON-Liste nutzt dieselben stabilen IDs und Statuswerte wie der Bericht.
Der Bericht vor der Behebung wird nach Beginn der Fixphase nicht umgeschrieben.

---

# 10. Bestätigte Lücken beheben

Behebe bestätigte, innerhalb des Auftrags sicher lösbare Probleme nach Priorität:

1. reproduzierenden Sicherheits- oder Regressionstest erstellen,
2. Ursache in der zentralen Business- oder Sicherheitsfunktion beheben,
3. alle ähnlichen Stellen im Projekt suchen,
4. Syntax-, Format- und relevante Anwendungstests ausführen,
5. ursprünglichen Reproduktionsweg erneut prüfen,
6. Befundstatus und Restrisiko aktualisieren,
7. kleine nachvollziehbare Änderungen und Commits vorbereiten.

Erhalte deutsche Bezeichnungen und vorhandene Strukturen. Baue keine lokale
Sonderprüfung nur für den gezeigten Endpunkt, wenn die Ursache zentral behoben
werden kann.

Halte an und fordere eine Freigabe an, wenn eine Behebung:

- Daten löscht oder migriert,
- öffentliche API-/MCP-Verträge bricht,
- Benutzer aussperren könnte,
- externe Infrastruktur oder Zugangsdaten ändern muss,
- fachliche Funktionen pauschal abschalten würde,
- nicht sicher getestet werden kann.

---

# 11. Regression und erneuter Sicherheitscheck

Teste nach den Fixes mindestens:

- Login, Logout, Sessionwechsel und Sperrung,
- Rollen, Rechte und negative Objektzugriffe,
- REST-API und MCP,
- Agentenverwaltung, Clientaufträge und Chat,
- Wiki, Upload und geschützten Download,
- Integrationen und Anbieterfehler,
- Cronjob und Doppelstartschutz,
- Speech-to-Text, Text-to-Speech und geschützte Medien,
- Jarvis-Maske,
- jeden bestätigten Befund,
- alle zuvor ausgeführten automatischen Prüfungen.

Dokumentiere exakte Befehle, Exit-Codes und Ergebnisse. Ein nicht ausführbarer
Test bleibt `nicht prüfbar`.

---

# 12. Abschlussbericht und Freigabe

Erzeuge:

```text
sicherheitsbericht_nach_behebung.md
```

Der Abschluss enthält:

- geprüften Commit und Zeitraum,
- Agent, Modell und verwendete Werkzeuge samt Version,
- Scope und ausgeschlossene Bereiche,
- Bedrohungsmodell,
- Befunde vor und nach der Behebung,
- konkrete Änderungen und Tests,
- Fehlalarme und Begründungen,
- offene, akzeptierte und nicht prüfbare Risiken,
- menschliche Freigabeentscheidung.

Technische Mindestbedingung für eine Freigabeempfehlung:

```text
keine offenen kritischen Befunde
keine ungeklärten hohen Befunde
alle Fixes reproduzierbar getestet
alle nicht geprüften Bereiche dokumentiert
Restrisiken ausdrücklich genannt
```

Formuliere höchstens:

> Im definierten Scope wurden nach den dokumentierten Verfahren keine weiteren
> bestätigten Befunde gefunden.

Eine menschliche Abnahme bleibt erforderlich.
