# Noob2Claw – Folge 19: Sicherheitszentrale, Angriffserkennung und IP-Firewall

# 1. Ziel

Noob2Claw erhält im Einstellungsbereich den neuen Menüpunkt `Sicherheit`. Dort
werden sicherheitsrelevante Ereignisse zentral erfasst, verständlich dargestellt
und kontrolliert bearbeitet. Eine anwendungsseitige IP-Firewall kann eindeutig
identifizierte Quellen zeitlich begrenzt oder dauerhaft sperren. Eine bewusst
verwaltete Whitelist schützt freigegebene Verwaltungszugänge vor automatischen
Sperren.

Die Sicherheitszentrale ergänzt Webserver-, Betriebssystem- und
Infrastrukturmaßnahmen. Sie ist kein Ersatz für Updates, sichere
Anwendungskontrollen oder eine vorgeschaltete Firewall.

# 2. Zentrale Architektur

Bestehende Endpunkte und Fachfunktionen melden Ereignisse an genau einen
zentralen Sicherheitsdienst. In `index.php`, `api.php`, `mcp.php`, `cron.php`
oder Navigationsdateien entsteht keine parallele Firewall- oder Logginglogik.
Der Menüpunkt wird über die vorhandenen Navigationsmetadaten registriert. SQL und
Business-Logik bleiben in zentralen Funktionen unter `inc/`; Webaktionen laufen
über `api.php`, und Schemaänderungen erfolgen als idempotente Migrationen.

```text
Web, API und MCP
      ↓
vertrauenswürdige Client-IP ermitteln
      ↓
Whitelist und aktive Sperren prüfen
      ↓
Authentifizierung, Rechte, Rate-Limit und Eingabeprüfung
      ↓
zentral normiertes Sicherheitsereignis speichern
      ↓
Sicherheitszentrale, Benachrichtigung und Auswertung
```

Firewallentscheidungen werden vor teurer Geschäftslogik getroffen. Fachliche
Autorisierung, CSRF-Schutz, Validierung und Rate-Limits bleiben trotzdem immer
erforderlich.

# 3. Vertrauenswürdige Client-IP

Die Anwendung verwendet nicht blind `X-Forwarded-For`, `Forwarded`,
`X-Real-IP` oder andere vom Client sendbare Header.

Verbindlich:

- Standardquelle ist die tatsächliche Socket-Adresse `REMOTE_ADDR`.
- Proxy-Header werden nur ausgewertet, wenn `REMOTE_ADDR` zu einer serverseitig
  konfigurierten Liste vertrauenswürdiger Reverse Proxys gehört.
- Die Proxy-Kette wird nach einem dokumentierten, getesteten Verfahren
  ausgewertet; beliebige linke Headerwerte gelten nicht automatisch als Client.
- IPv4 und IPv6 werden mit systemeigenen IP-Funktionen validiert und kanonisch
  gespeichert.
- IPv4-mapped IPv6-Adressen werden konsistent normalisiert.
- Ungültige oder mehrdeutige Werte führen nicht zu einer Sperre fremder IPs.
- Die vertrauenswürdige Proxy-Liste ist Serverkonfiguration und nicht über ein
  frei editierbares Browserfeld steuerbar.

Jedes Ereignis speichert getrennt die ermittelte Client-IP und, soweit für die
Diagnose erforderlich, die direkte Proxy-/Socket-IP. Ungeprüfte Headerketten
werden nicht vollständig in Oberfläche oder Log übernommen.

# 4. Sicherheitsereignisse

Erfasst werden mindestens:

- fehlgeschlagene und gesperrte Anmeldungen,
- ungültige oder deaktivierte API-/MCP-Tokens,
- fehlende Rechte und abgewiesene Objektzugriffe,
- CSRF-Verstöße,
- ausgelöste Rate-Limits,
- blockierte IPs,
- auffällige Eingaben und abgewiesene Uploads,
- Pfadmanipulations-, SQLi-, XSS-, SSRF- und Command-Injection-Indikatoren,
- ungültige Datei-, Klassen-, Methoden- oder Aktionsnamen,
- verdächtige Agenten- oder Toolaufrufe,
- Änderungen an Firewall, Whitelist und Sicherheitseinstellungen.

Ein Ereignis enthält mindestens:

```text
id
ereignis_typ
schweregrad
status
quelle
client_ip
proxy_ip
benutzer_id_optional
api_token_id_optional
route_oder_aktion
http_methode
korrelations_id
kurzbeschreibung
bereinigte_details_json
anzahl
erstmals_am
zuletzt_am
erstellt_am
```

Passwörter, Tokenwerte, Session-IDs, Cookies, Authorization-Header,
vollständige Request-Bodys und sensible Nutzdaten werden niemals gespeichert.
Wiederholte gleichartige Ereignisse dürfen innerhalb eines kurzen Zeitfensters
atomar zusammengefasst werden, damit Angreifer die Datenbank nicht durch
Logfluten füllen können.

# 5. Menüpunkt „Sicherheit“

Der Menüpunkt ist nur mit einem eigenen Leserecht sichtbar. Die Übersichtsseite
zeigt:

- Ereignisse der letzten 24 Stunden und deren Entwicklung,
- offene Ereignisse nach Schweregrad,
- häufigste Ereignistypen und Quellen,
- aktive temporäre und dauerhafte IP-Sperren,
- letzte Änderungen an Firewall und Whitelist,
- Zustand von Aufbewahrung und Hintergrundbereinigung.

Unterseiten:

```text
Sicherheit
├── Übersicht
├── Ereignisse
├── IP-Firewall
├── Whitelist
├── Auditberichte
└── Einstellungen
```

Die Ereignisliste unterstützt sichere Filter nach Zeitraum, Schweregrad, Typ,
Status, Quelle und IP. Detailansichten zeigen nur bereinigte Daten. CSV- oder
JSON-Exporte benötigen ein eigenes Recht, begrenzen den Zeitraum, werden
protokolliert und verhindern Formel-Injection.

Auditberichte erscheinen nur als redigierte Zusammenfassung und kontrollierter
Verweis auf ein nicht öffentlich ausgeliefertes Artefakt. Rohe Scannerlogs,
Exploitdetails und sensible Berichte werden nicht in die Weboberfläche kopiert.

Die Oberfläche bezeichnet einen Eintrag nur dann als „Angriff“, wenn dies
belegt ist. Unklare Signale heißen „verdächtiges Ereignis“. Ein fehlgeschlagener
Login allein ist kein Nachweis eines Angriffs.

# 6. IP-Firewall

Firewallregeln unterstützen IPv4, IPv6 und CIDR und enthalten mindestens:

```text
id
regel_typ: sperren | erlauben
ip_oder_cidr
grund
quelle: manuell | automatisch
aktiv_ab
aktiv_bis_optional
aktiv
erstellt_von_optional
ereignis_id_optional
erstellt_am
geaendert_am
```

Regelpriorität:

1. technisch notwendige interne Vertrauensregeln,
2. ausdrücklich aktive Whitelist-Regel,
3. aktive Sperrregel,
4. normale Anwendungsverarbeitung.

Jede Entscheidung wird über eine zentrale Funktion getroffen. CIDR-Prüfungen
verwenden binäre, getestete IP-Vergleiche; keine Präfix- oder Stringvergleiche.
Abgelaufene Regeln wirken nicht mehr und werden kontrolliert bereinigt.

Bei einer Sperre antwortet Web/API/MCP mit einem knappen geeigneten Status, ohne
interne Regeln, Schwellenwerte oder andere Adressen offenzulegen. Blockierte
Aufrufe werden aggregiert gezählt, nicht unbegrenzt einzeln gespeichert.

# 7. Automatische Sperren

Automatische Sperren sind standardmäßig konservativ und zeitlich begrenzt. Sie
dürfen nur auf serverseitig erfassten, hinreichend sicheren Signalen beruhen,
zum Beispiel einer hohen Zahl fehlgeschlagener Anmeldungen oder eindeutig
abgewiesener Tokenversuche innerhalb eines Zeitfensters.

Pflichtregeln:

- Schwellwert, Beobachtungsfenster, Sperrdauer und Obergrenze konfigurierbar,
- atomare Zähler und Entscheidungen gegen Race Conditions,
- exponentielle, aber begrenzte Sperrdauer bei Wiederholung,
- keine automatische dauerhafte Sperre,
- keine automatische Netzsperre aus einem einzelnen Clientereignis,
- Whitelist vor automatischer Sperre prüfen,
- erfolgreiche Anmeldung setzt nicht ungeprüft alle Sicherheitszähler zurück,
- jede automatische Regel verweist auf die auslösenden aggregierten Ereignisse,
- Simulation/Beobachtungsmodus ohne Blockierung für die erste Inbetriebnahme.

Signaturen oder Heuristiken dürfen nicht allein aufgrund frei gewählter Texte
pauschal sperren. Fehlalarme müssen als solche markierbar sein und in die
Schwellwertprüfung einfließen.

# 8. Whitelist

Die Whitelist heißt in technischen Bezeichnungen `allowlist`, bleibt in der
deutschen Oberfläche aber als „Whitelist“ verständlich. Sie unterstützt einzelne
IPv4-/IPv6-Adressen und bewusst freigegebene CIDR-Netze.

Eine Whitelist-Regel:

- umgeht nur IP-basierte Sperren und automatische IP-Sperren,
- umgeht niemals Login, Tokenprüfung, Rechte, CSRF, Objektberechtigungen oder
  Eingabevalidierung,
- benötigt Begründung und Ersteller,
- ist nach Möglichkeit zeitlich begrenzt,
- wird bei Anlage, Änderung, Deaktivierung und Löschung auditiert.

Vor dem Speichern zeigt die Oberfläche eine Warnung für große Netze. `0.0.0.0/0`
und `::/0` sind verboten. Loopback, private Netze und Proxy-Adressen werden nicht
automatisch freigegeben. Eine bestehende Sitzung reicht allein nicht aus, um die
aktuelle IP auf die Whitelist zu setzen.

# 9. Schutz vor Selbstaussperrung

Vor Aktivierung oder Änderung einer Sperrregel:

- aktuelle vertrauenswürdig ermittelte Client-IP anzeigen,
- Überschneidung mit der neuen Regel serverseitig prüfen,
- bei möglicher Selbstsperre eine ausdrückliche zweite Bestätigung verlangen,
- mindestens einen dokumentierten, nicht webbasierten Wiederherstellungsweg
  über Serverzugriff vorsehen.

Änderungen an vertrauenswürdigen Proxys, globalem Firewallstatus oder sehr großen
Netzen benötigen eine erhöhte Berechtigung und erneute Authentifizierung. Die
Anwendung darf niemals heimlich eine globale Whitelist anlegen.

Ein dokumentierter lokaler CLI-Notfallweg kann eine konkrete fehlerhafte Regel
deaktivieren. Er ist keine Web-Backdoor, benötigt Serverzugriff, verändert Regeln
atomar und erzeugt beim nächsten sicheren Anwendungsstart eine Auditspur.

# 10. Rechte und Audit

Mindestens folgende Rechte idempotent ergänzen:

```text
sicherheit_anzeigen
sicherheit_ereignisse_anzeigen
sicherheit_ereignisse_bearbeiten
sicherheit_firewall_verwalten
sicherheit_whitelist_verwalten
sicherheit_einstellungen_verwalten
sicherheit_berichte_anzeigen
sicherheit_berichte_exportieren
```

Nur Administratoren erhalten diese Rechte initial. Navigation ersetzt keine
serverseitige Rechteprüfung. Jede Änderung speichert Akteur, Zeit, Aktion,
betroffene Regel, vorherige und neue nicht sensitive Werte sowie Korrelations-ID.

# 11. Datenschutz und Aufbewahrung

IP-Adressen und Sicherheitsereignisse können personenbezogene Daten sein. Deshalb:

- Zweck, Rechtsgrundlage und Zugriffsberechtigte dokumentieren,
- nur erforderliche Daten speichern,
- kurze konfigurierbare Aufbewahrungsfristen nutzen,
- alte Ereignisse und abgelaufene Regeln in begrenzten Batches bereinigen,
- Exporte und Einsichtnahmen protokollieren,
- IPs nach Ende des operativen Zwecks löschen oder geeignet pseudonymisieren,
- Berichte vor Veröffentlichung redigieren.

Kritische Auditspuren dürfen nicht durch normale Bearbeiter verändert werden.
Fachliche Statusänderungen ergänzen die Historie, statt Originalereignisse
lautlos umzuschreiben.

# 12. Agenten-Audit und Behebung

OpenClaw oder Hermes prüft zusätzlich die gesamte Implementierung der
Sicherheitszentrale:

- alle Meldepfade nutzen denselben Sicherheitsdienst,
- Ereignisse enthalten keine Secrets oder vollständigen Requestdaten,
- Client-IP und Proxy-Vertrauen sind nicht spoofbar,
- IPv4-, IPv6- und CIDR-Vergleiche sind korrekt,
- Whitelist umgeht keine Authentifizierung oder Autorisierung,
- automatische Sperren sind atomar, begrenzt und reversibel,
- Oberfläche und Exporte sind gegen XSS, CSRF und Formel-Injection geschützt,
- Ereignisaggregation und Bereinigung verhindern ungebremstes Wachstum,
- Firewallprüfungen erzeugen keine Schleifen oder unvertretbare Last.

Bestätigte Lücken erhalten stabile IDs und werden nach dem Prozess aus
`1_Sicherheitsaudit.md` dokumentiert, getestet, zentral behoben und erneut
geprüft. Agenten dürfen Firewallregeln nicht eigenmächtig im Produktivsystem
aktivieren oder legitime Zugänge sperren.

# 13. Abnahmetests

Mindestens testen:

- Navigation und jedes einzelne Sicherheitsrecht,
- Ereigniserfassung für Web, REST-API und MCP,
- Redaction von Passwort, Token, Cookie und Session-ID,
- Aggregation wiederholter Ereignisse und Begrenzung der Datenmenge,
- echte Client-IP direkt und hinter ausschließlich vertrauenswürdigem Proxy,
- gefälschte Forwarding-Header von einem nicht vertrauenswürdigen Client,
- IPv4, IPv6, IPv4-mapped IPv6 und CIDR-Grenzen,
- manuelle temporäre Sperre, Ablauf und Entsperrung,
- konservative automatische Sperre im isolierten Test,
- Whitelist-Priorität ohne Umgehung anderer Sicherheitskontrollen,
- Verbot globaler Netze und Warnung bei großen Netzen,
- Selbstaussperrungsschutz und dokumentierter Wiederherstellungsweg,
- parallele Ereignisse und Firewallentscheidungen,
- sichere Filter, Detailansicht und Exporte,
- Aufbewahrung und Batchbereinigung,
- vollständige Auditspur jeder Verwaltungsänderung,
- Ausfall der Ereignisspeicherung ohne Preisgabe interner Fehler oder Umgehung
  der eigentlichen Sicherheitsentscheidung,
- normale Funktionen für nicht gesperrte Benutzer nach den Änderungen.

Aktive Angriffssimulationen laufen ausschließlich gegen eine freigegebene
isolierte Testinstanz. Produktion wird nicht für den Nachweis angegriffen.

# 14. Abschluss

Die Sicherheitszentrale ist fertig, wenn Ereignisse zentral und datensparsam
erfasst werden, berechtigte Administratoren sie auswerten können, IP-Regeln für
IPv4/IPv6/CIDR nachvollziehbar funktionieren, die Whitelist keine fachlichen
Sicherheitsprüfungen umgeht, Selbstaussperrung kontrolliert verhindert wird und
alle Abnahmetests sowie Auditberichte vorliegen.
