# Noob2Claw – Folge 14: Integrationsverwaltung und Open-Meteo

In Folge 14 wird Noob2Claw um eine zentrale, erweiterbare Integrationsverwaltung ergänzt. Externe Dienste werden als Klassen eingebunden und können mehrere getrennte Konfigurationseinträge besitzen.

# 🌐 Noob2Claw-Webseite

Weitere Informationen, Anleitungen und Neuigkeiten zum Projekt findet ihr auf [noob2claw.de](https://noob2claw.de).

---

# Ziel der Folge

Externe Dienste sollen nicht als einzelne Sonderlösungen eingebaut werden. Stattdessen entsteht ein einheitliches Integrationssystem mit:

- zentral registrierten und geprüften Integrationsklassen,
- mehreren Einträgen je Integration,
- flexiblen Einstellungen und eigenen Unterseiten,
- globalen Standardintegrationen je Fähigkeit,
- zentraler Cronjob-Ausführung,
- Protokollierung und Fehlerbehandlung.

Als erste Integration wird [Open-Meteo](https://open-meteo.com/en/docs) angebunden. Sie ruft stündlich aktuelle Wetterdaten für alle aktiven Open-Meteo-Einträge ab.

---

# Was wird umgesetzt?

- neuer Navigationspunkt „Integrationen“ im Einstellungsbereich
- verbindlicher Klassenvertrag und sichere Registry
- allgemeine Methoden `informationen()`, `einstellungen()`, `datenabholung()` und `cronjob()`
- integrationsspezifische Fähigkeiten wie `hole_wetter()`
- beliebig viele Einträge je Integrationsklasse
- Aktivierung, Konfiguration und Verbindungstest je Eintrag
- globale Auswahl einer Standardintegration je Systemfähigkeit
- Open-Meteo-Integration mit Standort- und Wetterkonfiguration
- Wahl zwischen kostenfreier und kommerzieller Open-Meteo-Nutzung einschließlich Attribution
- stündliche Wetterabholung und Speicherung des letzten gültigen Ergebnisses
- erster zentraler Noob2Claw-Cronjob mit Integrations-Dispatcher
- geschützte manuelle Ausführung und nachvollziehbare Logs
- manuelle Prüfung und reale Einrichtung des System-Cronjobs im Video

---

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger Arbeitsauftrag für den KI-Agenten
- `1_Integrationsverwaltung.md` – Architektur, Datenmodell, Klassenvertrag, Oberfläche, Open-Meteo, Cronjob, Sicherheit und Tests
- `2_Cronjob_Einrichtung.md` – vollständige Debian-Anleitung zur produktiven Einrichtung und Prüfung des zentralen Cronjobs

---

# Ablauf

```text
System-Cronjob
      │
      ▼
zentraler Integrations-Dispatcher
      │
      ├── fällige und aktive Einträge ermitteln
      ├── Integrationsklasse aus Registry laden
      ├── cronjob() der Integration aufrufen
      └── Lauf und Fehler protokollieren
                    │
                    ▼
             Open-Meteo API
                    │
                    ▼
          aktuelle Wetterdaten speichern
```

---

# Ergebnis

Nach dieser Folge besitzt Noob2Claw eine zentrale Grundlage für beliebig viele externe Dienste. Mehrere Server, Konten oder Standorte derselben Integration können getrennt verwaltet werden. Das Gesamtsystem kann je Fähigkeit einen konkreten Standard-Eintrag festlegen.
