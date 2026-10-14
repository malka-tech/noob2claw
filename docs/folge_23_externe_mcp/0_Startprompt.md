# Folge 23 – Implementierungsauftrag

Implementiere die externe MCP-Verwaltung nach allen Dokumenten dieses Ordners.
Prüfe zuerst den realen Anwendungscheckout und lies die Architektur aus Folge 9,
die MCP-Authentifizierung aus Folge 13, Sicherheit aus Folge 19 und den Mastercron
aus Folge 20. Bestehende Änderungen und native MCP-Tools erhalten.

1. Vorhandene Navigation, MCP-Registry, Zugang-Agent-Zuordnung, Secretspeicher,
   Audit und Cron-Dispatcher prüfen und wiederverwenden.
2. Additive Migrationen und die Masken für Server, Funktionen, Freigaben,
   Protokolle und Statistik erstellen. Keine destruktiven Migrationen.
3. Einen ausgehenden MCP-Client mit verhandeltem Protokoll und Streamable HTTP
   anbinden. Vorhandene geprüfte Bibliothek bevorzugen. Eigene PHP-Fachlogik
   bleibt prozedural; keine eigenen Klassen oder Shellbefehle aus Formularwerten.
4. Funktionskatalog vollständig laden, prüfen, versionieren und atomar aktivieren.
5. Präfixzuordnung und Agentenfreigaben in `tools/list` sowie `tools/call` integrieren.
6. Geschützte JSONL-Protokolle und Statistik mit Aufbewahrung einrichten.
7. Refresh, Bereinigung und Aggregation in den Mastercron einhängen.
8. Tests aus Dokument 3 zunächst mit einem lokalen Test-MCP-Server ausführen.
   ERP-Schreibaktionen nur in einer freigegebenen Sandbox mit Testdaten.

Vorgeschlagene zentrale Dateien, an vorhandene Namenskonventionen anpassen:

```text
inc/funktionen_mcp_extern_server.php
inc/funktionen_mcp_extern_tools.php
inc/funktionen_mcp_extern_rechte.php
inc/funktionen_mcp_extern_client.php
inc/funktionen_mcp_extern_proxy.php
inc/funktionen_mcp_extern_log.php
inc/funktionen_mcp_extern_statistik.php
```

`mcp.php` bleibt Protokoll, Authentifizierung und Routing. SQL liegt in zentralen
Funktionen, nicht in Masken oder Einstiegspunkten. Verwaltungsaktionen laufen
über die vorhandene `api.php` mit Sessionrechten und CSRF-Prüfung.
Keine externen Server automatisch aus Chatnachrichten konfigurieren.

Abgeben: Migrationen, UI, Adapter, Tests, Betriebsanleitung und Abnahmebericht
mit tatsächlich ausgeführten Prüfungen. Keine Secrets, ERP-Kundendaten oder
internen Videokonzepte ins Repository. Nicht automatisch committen oder pushen.
