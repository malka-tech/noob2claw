# Folge 23 – Verwaltung und Agentenrechte

## Eigener Navigationspunkt

Hauptpunkt **Externe MCP-Server**, getrennt von den eingehenden MCP-Zugängen.
Innerhalb des Bereichs: Übersicht, Server, Funktionen, Agentenfreigaben,
Protokolle und Statistik. Navigation über bestehende Metadaten registrieren:

```php
// TITLE: Externe MCP-Server
// ICON: plug
// BEREICH: MCP
// PRIO: 70
// RECHT: mcp_extern_anzeigen
```

Beispielmaske `nav/mcp_extern_uebersicht.php`; tatsächliche Bereichs- und
Iconunterstützung vorher prüfen. Kein isoliertes Menü außerhalb des Frameworks.

## Einrichtung in vier Schritten

1. **Server verbinden:** Name, MCP-Endpunkt, Präfix, Authentifizierung speichern.
   Verbindungstest macht nur Handshake und Katalogabfrage, keine ERP-Aktion.
2. **Funktionen prüfen:** Namen, Beschreibung, Eingabeschema und lokales Risiko
   ansehen. Neue Funktionen sind zunächst gesperrt.
3. **Agenten auswählen:** Pro Agent explizite Funktionen freigeben. Matrix mit
   Suche, Serverfilter und Sammelauswahl der aktuell sichtbaren Funktionen.
4. **Vorschau und aktivieren:** „Was sieht Agent X?“ anzeigen; Lesetest optional
   ausdrücklich auslösen. Server erst danach aktivieren.

Die Liste zeigt Name, Präfix, Status, Toolanzahl, freigegebene Anzahl, letzte
erfolgreiche Synchronisation, Katalogalter, letzten Fehler und Laufzeit.
Aktionen: Bearbeiten, Verbindung testen, Katalog aktualisieren, deaktivieren,
archivieren. Deaktivieren sperrt neue Aufrufe sofort. Bereits gesendete ERP-
Aufträge können dadurch nicht rückgängig gemacht werden.

Servereinstellungen: URL, aktiv, Credential-Referenz, Refreshintervall,
maximales Katalogalter, Verbindungs-/Aufrufzeitlimit, maximale Antwortgröße,
Parallelitätslimit, Logmodus und Aufbewahrungsdauer. Startwerte dieses Konzepts:
Refresh 15 Minuten, maximales Alter 24 Stunden, Verbindung 5 Sekunden,
Aufruf 60 Sekunden, Antwort 2 MiB, zwei parallele Aufrufe je Server.
Alle Grenzwerte mit plausiblen Obergrenzen validieren.

## Präfix und stabile Zuordnung

Beispiel: Präfix `erp`, externes Tool `kunden_suchen`, sichtbarer Name
`erp__kunden_suchen`. Präfix lokal eindeutig, unveränderlich nach Aktivierung;
Änderungen nur als angekündigte Migration. Lokale Regel: Präfix entspricht
`[a-z][a-z0-9_]{0,23}`. Gesamtnamen höchstens 128 ASCII-Zeichen, zulässige Zeichen
`A-Z`, `a-z`, `0-9`, `_`, `-`, `.`. Dies ist eine lokale Validierungsregel.

Mapping explizit speichern: öffentlicher Name → Server-ID + exakter externer Name.
Niemals beim Aufruf einfach am ersten Unterstrich teilen. Kollisionen mit nativen
oder anderen externen Tools ablehnen. Zu lange/ungeeignete Namen erfordern einen
explizit vergebenen lokalen Alias; nicht still kürzen oder normalisieren.

## Rechte bei jedem Zugriff

Erlaubnis ist die Schnittmenge aus aktivem MCP-Zugang, fest zugeordnetem aktivem
Agenten, dessen Toolfreigabe, Zugangsbeschränkungen, aktivem Server und aktuell
freigegebener Toolversion. Für externe Tools immer Agentenidentität verlangen,
auch für lesende Funktionen. Nicht freigegebene Tools weder auflisten noch aufrufen.

Der Agent-ID-Suffix aus Folge 13 ist allein kein Identitätsnachweis. Für diese
Weiterleitung jeden Agenten mit einem eigenen Zugang verbinden, der nur genau
diese Agent-ID zulässt. Ein geteilter Token mit mehreren auswählbaren Agenten
würde deren Trennung umgehen; solche Zugänge nicht für externe Tools freischalten.
Agent-ID niemals aus Toolargumenten übernehmen.

Neue Tools nicht automatisch erlauben. Sammelauswahl speichert konkrete IDs,
keinen dauerhaften Wildcard. Entfernte Tools sperren. Änderungen an Schema,
Beschreibung oder Risiko erfordern erneute Freigabe; bisherige Freigaben dürfen
keine geänderte Funktion ungeprüft autorisieren. Entzug wirkt auch bei gecachtem
Katalog sofort und wird direkt vor dem Versand erneut geprüft.

Unbekannte Funktionen als schreibend behandeln. Externe Risiko-Annotations sind
Hinweise, keine Autorisierung. Lokale Einstufung: lesend, schreibend, destruktiv.
Destruktive und kritische Buchungsaktionen brauchen Einzelfreigabe durch einen
berechtigten Menschen: gebunden an Agent, Toolversion, kanonische Argumente,
Ablaufzeit und einmalige Verwendung. Der Agent darf sich diese nicht selbst geben.
Bei ERP-Tools mit Mandantenparametern zulässigen Mandanten serverseitig erzwingen;
Toolrechte allein ersetzen keine fachliche Datensatzberechtigung im ERP.

Webrechte getrennt vergeben: Anzeigen, Server verwalten, Geheimnisse ändern,
Freigaben verwalten, Tests auslösen, Logmetadaten lesen, Payload lesen/exportieren,
Statistik lesen. Masken und API-Aktionen prüfen dieselben Rechte.

## Datenmodell

Vorhandene Tabellen erweitern, wenn sie den Vertrag erfüllen. Additiv und ohne
NULL nach Projektkonvention, IDs und Zeitstempel eindeutig typisieren.

| Bereich | Wesentliche Felder und Eindeutigkeiten |
| --- | --- |
| Server | ID, Name, Präfix unique, URL, Secretreferenz, Aktivstatus, Konfigurationsrevision, Limits |
| Katalogversion | Server, Credentialrevision, Versions-ID, Hash, erfolgreich_abgerufen_am, gültig_bis, Status |
| Tools | Server, externer Name, öffentlicher Name unique, Schema, Beschreibung, Hash, Risiko, Status |
| Agentenfreigabe | Agent, Tool, genehmigter Hash, erlaubt; unique Agent/Tool |
| Aufruf | Request-ID unique, Agent, MCP-Zugang, Server, Toolversion, Status, Zeiten, Bytes, Logreferenz |
| Einzelfreigabe | Aufrufbindung, Argumenthash, Genehmiger, Ablauf, Verbrauchsstatus |
| Statistik | Zeitfenster, Server, Tool, Agent, Zähler und Laufzeitverteilung |

Audits behalten Namens-/Präfix-Snapshots. Server mit Historie archivieren statt
Statistik durch kaskadierendes Löschen zu verlieren. Migrationen wiederholbar
ausführen und bei Rückbau Daten sichern, nicht automatisch Tabellen löschen.
