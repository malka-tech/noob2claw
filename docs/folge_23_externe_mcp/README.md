# Noob2Claw – Folge 23: Externe MCP-Server wie ein ERP anbinden

Noob2Claw erhält den eigenen Navigationspunkt **Externe MCP-Server**. Ein
Administrator hinterlegt beispielsweise einen ERP-MCP-Server, vergibt ein Präfix
und erlaubt ausgewählte Funktionen für bestimmte Agenten. Die Agenten verwenden
weiter ihren vorhandenen Noob2Claw-MCP-Zugang; externe Zugangsdaten bleiben im Backend.

```text
Agent → Noob2Claw /mcp.php → Rechteprüfung und Toolzuordnung → externer MCP-Server
                         ↘ geschütztes Protokoll und Statistik
```

Der Funktionskatalog wird gecacht. Ein tatsächlicher Toolaufruf wird nach erneuter
Rechteprüfung live ausgeführt. Ergebnisse und ERP-Geschäftsdaten werden nicht
automatisch gecacht. Die Weiterleitung ist damit eine kontrollierte Vermittlung,
kein ungefilterter Netzwerkproxy.

## Unterlagen

- [0_Startprompt.md](0_Startprompt.md): Auftrag für die Implementierung.
- [1_Verwaltung_und_Rechte.md](1_Verwaltung_und_Rechte.md): Navigation, Bedienung,
  Präfixe, Agentenfreigaben und Datenmodell.
- [2_Weiterleitung_und_Cache.md](2_Weiterleitung_und_Cache.md): MCP-Vertrag,
  Cacheverhalten, Fehler und Netzwerkschutz.
- [3_Protokolle_Statistik_Abnahme.md](3_Protokolle_Statistik_Abnahme.md): Logdatei,
  Datenansicht, Kennzahlen und überprüfbare Testfälle.

Dies sind Umsetzungsunterlagen, keine bereits bereitgestellte Software. Die Folge
erweitert Folge 13 und den Mastercron aus Folge 20. Sie ist unabhängig von der
Audio- und Webseitenfolge. Keine neuen öffentlichen MCP-Endpunkte und keine
zusätzlichen Betriebssystem-Cronjobs. Veröffentlichung erst nach Freigabe.
