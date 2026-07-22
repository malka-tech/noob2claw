# Noob2Claw – Folge 13: MCP-Funktionen für das Wiki

In Folge 13 wird das Wiki aus Folge 12 über das Model Context Protocol (MCP) für OpenClaw und andere autorisierte Agenten zugänglich gemacht.

# 🌐 Noob2Claw-Webseite

Weitere Informationen, Anleitungen und Neuigkeiten zum Projekt findet ihr auf [noob2claw.de](https://noob2claw.de/).

---

# Ziel der Folge

KI-Agenten sollen Wiki-Inhalte sicher suchen, laden, anlegen und bearbeiten können. Dabei werden vorhandene Rechte, Versionierung und Logging-Strukturen des Noob2Claw-Systems weiterverwendet.

---

# Was wird umgesetzt?

- MCP-Verwaltungsmasken im Webinterface
- sichere MCP-Zugänge und Token-Erzeugung
- Zuordnung authentifizierter Agenten
- Wiki-Suche und Laden einzelner Artikel
- Anlegen und Bearbeiten von Wiki-Artikeln
- Datei-Uploads über MCP
- Zugriff auf die Versionshistorie
- Agentennachweis in Wiki-Versionen
- MCP-Rechteprüfung und Protokollierung
- Konfiguration und Funktionstest mit OpenClaw

---

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
- `1_MCP_Defintion.md` – MCP-Architektur, Authentifizierung, Tools, Rechte, Versionierung und Tests

---

# Ablauf

```text
OpenClaw-Agent
      │
      │ MCP-Anfrage mit Zugang und Agent-ID
      ▼
Noob2Claw MCP-Server
      │
      ├── Rechteprüfung
      ├── Wiki-Funktion ausführen
      └── Zugriff protokollieren
      │
      ▼
Wiki-Inhalt oder Ergebnis
```

---

# Ergebnis

Nach dieser Folge können autorisierte Agenten das Noob2Claw-Wiki kontrolliert als Wissensquelle verwenden und Inhalte bearbeiten. Änderungen bleiben durch Rechteprüfung, Agentenzuordnung, Versionierung und Logging nachvollziehbar.
