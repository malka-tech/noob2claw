# Noob2Claw – Folge 10: Agentenverwaltung und Clients

In Folge 10 entsteht die technische Grundlage, um externe KI-Agenten zentral über das Noob2Claw-Webinterface zu verwalten und mit Aufgaben zu versorgen.

# 🌐 Noob2Claw-Webseite

Weitere Informationen, Anleitungen und Neuigkeiten zum Projekt findet ihr auf [noob2claw.de](https://noob2claw.de/).

---

# Ziel der Folge

Das Noob2Claw Core-System wird um eine Agenten- und Clientverwaltung erweitert. Externe Clients melden sich über eine abgesicherte API am Core an, holen Aufträge ab, führen sie mit der verfügbaren KI-CLI aus und übertragen Ergebnisse sowie Statusinformationen zurück.

---

# Was wird umgesetzt?

- Verwaltung von Agenten und zugehörigen Clients
- zentrale Auftragsverwaltung
- authentifizierte Agenten-API
- Heartbeats, Statusmeldungen und Hardwareinformationen
- Schutz vor doppelter Auftragsverarbeitung
- Logging und Prozessüberwachung
- Clientbetrieb als systemd-Dienst
- kontrollierter Neustart des Clients
- Updatefunktion für Clientdateien
- Nutzung bestehender Benutzer- und Rechteverwaltung

---

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
- `1_Agentenverwaltung_und_Client.md` – Architektur, Datenmodell, API, Clientlogik, Sicherheit und Tests

---

# Ablauf

```text
Noob2Claw Core
       │
       │ Agenten-API
       ▼
Externer Agenten-Client
       │
       │ Auftrag ausführen
       ▼
OpenClaw / Hermes / Codex / Claude
       │
       │ Ergebnis und Status
       ▼
Noob2Claw Core
```

---

# Ergebnis

Nach dieser Folge können externe Agenten-Clients zentral registriert, überwacht und mit Aufträgen versorgt werden. Damit ist die Grundlage für den Agentenchat aus Folge 11 geschaffen.
