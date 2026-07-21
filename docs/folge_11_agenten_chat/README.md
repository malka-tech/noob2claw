# Noob2Claw – Folge 11: Agentenchat

In Folge 11 wird auf Basis der Agenten- und Clientverwaltung aus Folge 10 ein vollständiger Chat zwischen Benutzern und externen KI-Agenten entwickelt.

---

# Ziel der Folge

Benutzer sollen im Noob2Claw-Webinterface einen Agenten auswählen, Nachrichten und Dateien senden und Antworten übersichtlich in einem Chatverlauf empfangen können.

---

# Was wird umgesetzt?

- Agentenliste mit Statusanzeige
- Chatoberfläche mit Nachrichtenverlauf
- Benutzer-, Agenten- und Systemnachrichten
- neue Chats, Archivierung und Statusverwaltung
- Datei-Uploads und Agentenbilder
- Markdown- und Codeausgabe
- API-Aktionen für sämtliche Chatfunktionen
- Verbindung zur bestehenden Agenten-Auftragsverwaltung
- Polling für neue Nachrichten und Statusänderungen
- Rechteprüfung, Upload-Sicherheit und Logging

---

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
- `1_Agentenchat.md` – UI, Datenmodell, API, Auftragsanbindung, Sicherheit und Tests

---

# Ablauf

```text
Benutzer schreibt Nachricht
          │
          ▼
Noob2Claw Agentenchat
          │
          ▼
Agenten-Auftragsverwaltung
          │
          ▼
Externer Agenten-Client
          │
          ▼
Antwort im Chatverlauf
```

---

# Ergebnis

Nach dieser Folge steht ein zentraler Agentenchat bereit, der die vorhandenen Clients nutzt und Gespräche, Dateien, Statusinformationen und Agentenantworten in einer gemeinsamen Oberfläche zusammenführt.

