# Folge 23 – Weiterleitung und Funktionscache

## Protokollumfang

Noob2Claw bleibt nach außen MCP-Server und ist zum externen System MCP-Client.
Beide Verbindungen haben getrennte Authentifizierung, Sessions und Request-IDs.
Das erste Profil unterstützt Streamable HTTP mit HTTPS und administrativ
hinterlegtem Bearer-Token. OAuth-only-Server benötigen einen gesondert geprüften
OAuth-Client; sie werden hier ausdrücklich als nicht unterstützt angezeigt.
Kein beliebiges Headerformular, kein Token-Passthrough und kein `stdio`-Start
über ein Webformular. Altes HTTP/SSE-Transportprofil nicht mit SSE-Antworten von
Streamable HTTP verwechseln; Legacy-Unterstützung ist nicht Teil dieser Folge.

Der Client initialisiert die Verbindung, prüft Version und Fähigkeiten und
sendet anschließend die Initialisierungsbenachrichtigung. Unvereinbare Versionen
werden abgelehnt. Siehe [MCP Lifecycle](https://modelcontextprotocol.io/specification/2025-11-25/basic/lifecycle).

HTTP-Antworten können JSON oder SSE enthalten. Ein verwendeter Client muss beide
Fälle, Protokollheader und gegebenenfalls Session-IDs korrekt behandeln, nicht
einfach jede Antwort als JSON dekodieren. Siehe [MCP Transports](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports).

Der Katalog verwendet `tools/list` einschließlich Pagination. Aufrufe verwenden
`tools/call`. Toolergebnisse können `content`, `structuredContent` und `isError`
enthalten. `notifications/tools/list_changed` signalisiert mögliche Änderungen.
Siehe [MCP Tools](https://modelcontextprotocol.io/specification/2025-11-25/server/tools).

Resources, Prompts, Sampling, Elicitation und taskpflichtige Tools werden nicht
automatisch weitergeleitet. Nicht unterstützte Fähigkeiten nicht annoncieren.
Ein Tool mit zwingenden nicht unterstützten Anforderungen bleibt gesperrt.
Zurückgegebene Resource-Links nicht automatisch öffnen oder nachladen; lokale
Resource-URIs des ERP sind keine Noob2Claw-Dateien. Unterstützte Ergebnisblöcke
korrekt erhalten, nicht pauschal in einen Textstring umwandeln. Nicht unterstützte
Inhalte mit kontrolliertem Fehler statt verfälschter Erfolgsmeldung beantworten.

## Aufrufablauf

1. Eingehenden Zugang und gebundene Agentenidentität prüfen.
2. Tool anhand der gespeicherten Registry laden, nie aus frei übergebenem Host.
3. Aktuelle Freigaben, Serverstatus, genehmigten Hash und Katalogalter prüfen.
4. Argumente mit der freigegebenen JSON-Schema-Version validieren. Unzulässige
   Argumente ablehnen, niemals still entfernen. Schema-Referenzen nicht aus dem
   Internet nachladen; nicht sicher unterstützte Schemas nicht freischalten.
5. Rate-/Parallelitätslimit und gegebenenfalls Einzelfreigabe prüfen. Sicheren
   Audit-Start schreiben und vor Versand Sperren/Rechte nochmals prüfen.
6. Externen Originalnamen und validierte Argumente über separate Credentials
   senden. Downstream-Token niemals weitergeben. JSON-RPC-IDs intern korrelieren.
7. Antwort begrenzen, prüfen und dem ursprünglichen Aufruf korrekt zuordnen.
   Protokollfehler, Toolfehler und fachlich unklaren Ausgang unterscheiden.
8. Ergebnis und Statistik abschließen. Keine internen Stacktraces ausgeben.

Sessionzustand mindestens nach Server, Credentialrevision und Agent trennen.
Browser oder Agent dürfen keine Upstream-Session-ID auswählen. Bei Tokenrotation
alte Sessions schließen und Katalog neu verifizieren. Eine abgelaufene Session
erlaubt einen neuen Handshake, aber keine automatische Wiederholung eines bereits
gesendeten Schreibauftrags.

Unbekannter Ausgang nach Timeout: `ausgang_unbekannt`, keine automatische
Wiederholung. Optional vorhandene ERP-Statusabfrage mit Korrelations-ID nutzen.
Selbst JSON-RPC-Request-IDs sind keine Idempotenzgarantie. Automatische Wiederholung
von lesenden Aufrufen erst mit lokal bestätigter Nebenwirkungsfreiheit und engem
Retry-Budget. Abbruch des Clients garantiert keine Rücknahme im ERP.

## Cache mit kontrollierter Freigabe

Gecacht werden Definitionen, nicht Ergebnisse. Cachekey enthält Server-ID,
Endpoint-/Konfigurationsrevision, Credentialkontext und verhandelte Version.
Die nach Rechten gefilterte Agentenliste nicht global wiederverwenden.

Normaler Agentenaufruf von `tools/list` liest den lokalen Snapshot und filtert
aktuelle Rechte. Er fragt keinen externen Server live ab. Fehlt ein Katalog,
bleibt der Server unverfügbar und ein Refresh wird angefordert. Keine Anfrage-
Warteschlange mit unbegrenzt vielen Refreshjobs anlegen.

Refresh manuell, nach gespeichertem Intervall oder nach List-Changed-Hinweis.
Mit einer zeitlich begrenzten DB-Lease pro Server bündeln. Alle Seiten zunächst
in einen Kandidatensnapshot laden; Toolanzahl, Antwortgröße, Schematiefe und
wiederholte Cursor begrenzen. Bei fehlerhafter oder unvollständiger Liste bleibt
der letzte erfolgreiche Snapshot unverändert. Eine erfolgreich vollständig
geladene leere Liste dagegen entfernt alle Tools.

Atomarer Wechsel nach vollständiger Prüfung: unveränderte Definitionen behalten
ihre Freigabe, neue/geänderte gehen in „Freigabe erforderlich“, entfernte in
„nicht mehr verfügbar“. Beschreibungen und Schemas in den genehmigten Hash
einbeziehen. Externe Inhalte sind untrusted; Beschreibungen enthalten keine
autorisierenden Anweisungen an Agenten oder Administratoren.

Bei temporärem Refreshfehler den alten Snapshot bis maximal 24 Stunden verwenden,
Status „veraltet“ sichtbar machen. Danach alle externen Aufrufe dieses Servers
sperren. Authentifizierungsfehler, Endpoint-/Credentialwechsel oder Deaktivierung
sperren sofort, unabhängig vom Alter. Ein alter Katalog garantiert nicht, dass
ein Server unveränderten Code ausführt: sensible ERP-Zugänge zusätzlich auf
Upstream-Seite beschränken und Anbieteränderungen kontrollieren.

Lokale Katalogänderungen den verbundenen Clients protokollgerecht mitteilen,
soweit unterstützt; sonst erneute Toolabfrage/Verbindung in der UI verlangen.
Stale Clientlisten sind nie eine Berechtigung für inzwischen gesperrte Aufrufe.

## Mastercron und Netzwerkschutz

Dispatcher `mcp_extern_wartung` in `cron.php alle` registrieren. Er plant fällige
Refreshes, Rotation, Löschung und Statistik mit begrenztem Arbeitsbudget und
Fehlerisolierung. Lange MCP-Verbindungen dürfen die übrigen Dispatcher nicht
blockieren; vorhandenen Jobworker verwenden oder eng begrenzte Einzeljobs.
Keinen zweiten Crontab-Eintrag und kein `flock`-Kommando ergänzen.

Nur explizit erlaubte Hosts/Ports, kein URL-Userinfo, keine Redirects, keine
ungeprüften DNS-Zielwechsel. Loopback, Link-local und Metadatenendpunkte sperren.
Ein internes ERP braucht eine eng definierte Host-/IP-Ausnahme, nicht die globale
Freigabe aller privaten Netze. TLS prüfen. Selbstaufrufe des eigenen Gateways
sperren; zyklische Gateway-Ketten nicht zulassen. Auflösung und tatsächlich
verbundene IP gegen die Zielregel prüfen.

Credentials verschlüsselt über vorhandenen Secretspeicher, nicht nur hashen:
der ausgehende Client muss sie nutzen können. Schlüssel getrennt halten.
Administrativ konfigurierten Zielserver nie durch Toolargumente überschreiben.
