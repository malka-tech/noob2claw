# Noob2Claw – Folge 19: Sicherheitsaudit, Reporting und Behebung

# 1. Ziel und Grundsatz

Der Sicherheitscheck untersucht nicht nur einzelne Dateien, sondern die gesamte
Angriffsfläche und ihre Vertrauensgrenzen. Automatische Scanner schaffen Breite;
manuelle Datenfluss-, Rechte- und Geschäftslogikprüfungen schaffen Tiefe.

Ein Werkzeugfund ist ein Hinweis. Ein bestätigter Befund benötigt einen
nachvollziehbaren Codepfad, realistische Voraussetzungen, eine konkrete
Auswirkung und einen sicheren Reproduktionsweg.

# 2. Vier getrennte Phasen

## Phase A – Nur lesen

- Projektstand und Scope erfassen,
- Architektur und Angriffsfläche kartieren,
- Werkzeuge und Versionen inventarisieren,
- automatische und manuelle Prüfungen durchführen,
- Befunde bestätigen und entdoppeln.

## Phase B – Bericht vor Behebung

- stabile Befund-IDs vergeben,
- Risiko und Priorität bewerten,
- Fehlalarme und nicht prüfbare Bereiche dokumentieren,
- unveränderte Ausgangsbasis sichern.

## Phase C – Behebung

- eigener Branch,
- Test vor Fix,
- kleine zentrale Ursachenbehebung,
- Prüfung ähnlicher Stellen,
- nachvollziehbare Commits.

## Phase D – Nachprüfung

- Reproduktion erneut ausführen,
- Funktionsregression testen,
- automatische Prüfungen wiederholen,
- Abschlussbericht und Restrisiko erstellen.

# 3. Bedrohungsmodell

Zu betrachten sind mindestens:

| Bereich | Schutzwert | typischer Missbrauch |
|---|---|---|
| Web | Session und Benutzerkonto | CSRF, XSS, Auth-Bypass |
| API | Daten und Aktionen | fehlende Rechte, BOLA/IDOR |
| MCP | Tools und Agentenkontext | Tokenmissbrauch, Rechteausweitung |
| Dateien | Uploads und Medien | Ausführung, Traversal, fremder Download |
| Integrationen | Anbieterzugänge | SSRF, Secret-Leak, Kostenmissbrauch |
| Chat/KI | Nachrichten und Tools | Prompt Injection, fremde Chat-ID |
| Cron | Hintergrundaufträge | HTTP-Aufruf, Doppelverarbeitung |
| Datenbank | Geschäfts- und Personendaten | SQL-Injection, zu breite Rechte |
| Sicherheitszentrale | Ereignisse und IP-Regeln | IP-Spoofing, Lockout, Logflut |

# 4. Prüfkatalog

## Architektur

- nur `api.php` als REST- und `mcp.php` als MCP-Einstieg,
- Business-Logik zentral unter `inc/`,
- kein SQL in `nav/`, `api.php` oder `mcp.php`,
- keine doppelte Rechte-, Logging-, Upload- oder Schedulerlogik,
- sichere Allowlist für Seiten, Module, Aktionen, Klassen und Methoden.

## Authentifizierung

- Passwort-Hashverfahren und sichere Verifikation,
- Session-Fixation, Cookieattribute, Timeout und Logout,
- API-/MCP-Tokens nur gehasht, Klartext nur einmal bei Erzeugung,
- Rotation, Deaktivierung und Sperrung,
- Rate-Limits und sichere Fehlermeldungen ohne Benutzerauflistung.

## Autorisierung und Objekte

Für jede Aktion sind getrennt zu prüfen:

```text
Authentifizierung
→ allgemeines Recht
→ konkrete Objektberechtigung
→ validierte Aktion
```

Negative Tests verwenden mindestens zwei normale synthetische Benutzer. Benutzer
B darf die IDs von Benutzer A nicht lesen, verändern, exportieren oder löschen.

## Eingabe und Ausgabe

- Parameterbindung für SQL-Werte,
- feste Allowlist für nicht bindbare SQL-Strukturen,
- keine Shellbefehle aus Benutzereingaben,
- kontextgerechtes Escaping,
- CSRF-Schutz für schreibende Webaktionen,
- JSON- und Dateischemata serverseitig validieren,
- technische Fehler nur intern protokollieren.

## Upload und Download

- Größe, Endung, MIME und Signatur gemeinsam prüfen,
- zufällige interne Namen,
- Originalname nur als bereinigte Metadaten,
- Uploadbereich nicht ausführbar,
- kein direkter ungeschützter Dateipfad,
- Download mit Objektberechtigung und sicheren Headern,
- Aufbewahrung und Bereinigung.

## Externe Verbindungen

- feste Ziele und HTTPS,
- SSRF-Schutz einschließlich DNS, Redirects, privaten Netzen und Metadatenzielen,
- Timeouts, maximale Antwortgröße und Schemaprüfung,
- Rate-Limits, Backoff und Kostengrenzen,
- keine Secrets in URL, Browser oder Logs.

## Agenten und Prompt Injection

- KI-Inhalt ist untrusted input,
- Toolaufruf benötigt explizite technische und fachliche Berechtigung,
- Modelltext darf keine Sicherheitsentscheidung ersetzen,
- Datei-, Shell-, SQL- und Netzwerkparameter werden unabhängig validiert,
- Agent darf nur freigegebene Daten und Objekte sehen,
- Anweisungen aus Benutzerdateien dürfen Systemgrenzen nicht verändern.

## Cron

- direkter CLI-Einstieg `/var/www/noobclaw/cron.php`,
- kein `/public/`-Unterordner und keine öffentliche Cron-URL,
- HTTP-Aufruf wird abgelehnt,
- feste CLI-Aufgaben-Allowlist,
- globale atomare Anwendungssperre,
- atomare Eintrag-Claims mit TTL und sicherer Freigabe,
- definierte Exit-Codes und isolierte Einzelfehler,
- keine Secrets in Cronzeile oder Log.

## Sicherheitszentrale und IP-Firewall

- ein zentraler Ereignis- und Firewalldienst statt Prüfungen in einzelnen Seiten,
- `REMOTE_ADDR` als Standard und Proxy-Header nur von vertrauenswürdigen Proxys,
- korrekte Normalisierung und binäre Prüfung von IPv4, IPv6 und CIDR,
- keine Passwörter, Tokens, Sessions, Cookies oder vollständigen Bodys im Ereignis,
- Aggregation, Rate-Limit und begrenzte Aufbewahrung gegen Logfluten,
- Whitelist umgeht nur IP-Sperren und niemals Authentifizierung oder Rechte,
- automatische Sperren konservativ, atomar, zeitlich begrenzt und reversibel,
- Schutz vor Selbstaussperrung und dokumentierter Wiederherstellungsweg,
- eigene Rechte und Auditspur für Ansicht, Export und Regeländerungen.

# 5. Toolstrategie

Werkzeuge werden nie blind vertraut. Für jedes Werkzeug werden festgehalten:

- Name und Version,
- Konfiguration und Regelwerk,
- ausgeführter Befehl,
- Scope und Ausschlüsse,
- Exit-Code,
- rohe Fundzahl,
- bestätigte Befunde,
- Fehlalarme,
- nicht geprüfte Bereiche.

Geeignete Kategorien sind PHP-Syntax, PHP-/JavaScript-Analyse,
Abhängigkeitsprüfung, Secret-Scan, SAST und ein passiver lokaler Webscan. Eine
Installation oder Änderung der Projektabhängigkeiten benötigt vorherige
Freigabe.

# 6. Schweregrad und Reihenfolge

| Priorität | Beispiele | Behandlung |
|---|---|---|
| kritisch | RCE, aktiver Auth-Bypass, echte Secret-Offenlegung | Freigabe blockieren, sofort isolieren |
| hoch | SQLi, Rechteausweitung, fremde Daten, gefährlicher Upload, SSRF | beheben oder ausdrücklich entscheiden |
| mittel | XSS, CSRF, Sessionproblem, sensible Informationslecks | geplant beheben und testen |
| niedrig | begrenzte Härtungslücke | dokumentieren und einplanen |
| informativ | Verbesserung ohne konkrete Ausnutzung | transparent festhalten |

CVSS kann ergänzen. Die tatsächliche Priorität berücksichtigt erreichbare Daten,
erforderliche Rechte, Angriffsweg, Auswirkung und vorhandene Schutzschichten.

# 7. Behebungsstandard

Jede Behebung erfüllt:

1. Befund ist vorab dokumentiert.
2. Ein Test oder sicherer Reproduktionsweg zeigt das Problem.
3. Die zentrale Ursache wird behoben.
4. Ähnliche Stellen werden gesucht.
5. Funktion und Sicherheit werden getestet.
6. Restrisiko und Kompatibilität werden bewertet.
7. Bericht und maschinenlesbare Liste werden aktualisiert.

Nicht zulässig sind reine Oberflächenkosmetik, das Ausblenden von Fehlermeldungen
ohne Ursachenbehebung oder das pauschale Abschalten benötigter Funktionen ohne
dokumentierte Entscheidung.

# 8. Reporting-Artefakte

Verbindliche Ergebnisse:

```text
sicherheitsbericht_vor_behebung.md
sicherheitsbericht_nach_behebung.md
sicherheitsbefunde.json
```

Diese Artefakte gehören standardmäßig nicht in ein öffentliches Webverzeichnis.
Ob und in welcher redigierten Form sie versioniert werden, entscheidet der
Verantwortliche nach Prüfung auf sensible Inhalte.

# 9. Freigaberegel

Eine technische Freigabeempfehlung ist nur möglich, wenn:

- keine kritischen Befunde offen sind,
- keine hohen Befunde ungeklärt sind,
- alle Fixes reproduzierbar geprüft wurden,
- Funktionsregressionen erfolgreich waren,
- nicht prüfbare Bereiche und Restrisiken dokumentiert sind.

Die endgültige Freigabe erfolgt durch einen verantwortlichen Menschen. Ein
Sicherheitscheck ist eine zeitpunkt- und scopebezogene Bewertung, keine Garantie.
