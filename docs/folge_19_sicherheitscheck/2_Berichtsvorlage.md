# Noob2Claw – Folge 19: Berichtsvorlage

Diese Vorlage wird für den Bericht vor und nach der Behebung verwendet. Sensible
Werte werden redigiert und niemals in den Bericht kopiert.

# 1. Metadaten

```text
Berichtstyp:                 vor Behebung | nach Behebung
Projekt:
Repository:
geprüfter Branch:
geprüfter Commit:
Prüfzeitraum:
Agent:
Agentenversion/Modell:
Testumgebung:
verantwortliche Person:
```

# 2. Management-Zusammenfassung

```text
Ziel der Prüfung:
Gesamteinschätzung:
größte bestätigte Risiken:
wesentliche Behebungen:
offene Restrisiken:
Freigabeempfehlung:
```

Keine pauschale Aussage wie „vollständig sicher“ verwenden.

# 3. Scope

## Geprüft

-

## Nicht geprüft

- Bereich:
  Grund:
  Risiko:
  empfohlener nächster Schritt:

# 4. Bedrohungsmodell

## Schutzwerte

-

## Angreifer und Voraussetzungen

-

## Vertrauensgrenzen

-

## wichtigste Missbrauchsfälle

-

# 5. Werkzeuge und Methoden

| Werkzeug/Methode | Version | Befehl/Umfang | Exit-Code | Ergebnis |
|---|---|---|---:|---|
| | | | | |

Fehlende oder nicht ausführbare Werkzeuge ausdrücklich als `nicht ausgeführt`
markieren.

# 6. Befundübersicht

| Schweregrad | offen | behoben | akzeptiert | Fehlalarm | nicht prüfbar |
|---|---:|---:|---:|---:|---:|
| kritisch | 0 | 0 | 0 | 0 | 0 |
| hoch | 0 | 0 | 0 | 0 | 0 |
| mittel | 0 | 0 | 0 | 0 | 0 |
| niedrig | 0 | 0 | 0 | 0 | 0 |
| informativ | 0 | 0 | 0 | 0 | 0 |

# 7. Einzelbefund

Für jeden Befund diesen vollständigen Block verwenden:

```text
ID:                         N2C-SEC-001
Titel:
Status:                     offen | bestätigt | in Behebung | behoben |
                            akzeptiertes Risiko | Fehlalarm | nicht prüfbar
Schweregrad:                kritisch | hoch | mittel | niedrig | informativ
Vertrauensniveau:           hoch | mittel | niedrig
CVSS-Version und Vektor:
CWE:
OWASP-ASVS-Anforderung:
OWASP-WSTG-Test:

betroffene Dateien:
betroffene Funktionen:
betroffene Einstiegspunkte:
Voraussetzungen:
technische Ursache:
Auswirkung:
sicherer Reproduktionsweg:
redigierter Beleg:
ähnliche geprüfte Stellen:
Behebungsvorschlag:
tatsächliche Behebung:
Regressionstest:
Testergebnis vor Behebung:
Testergebnis nach Behebung:
Restrisiko:
verantwortliche Entscheidung:
Commit der Behebung:
```

Dateien und Zeilen dürfen genannt werden. Secretwerte, echte Sessiondaten,
Personendaten und direkt missbrauchbare produktive Exploits werden nicht
aufgenommen.

# 8. Änderungen

| Befund-ID | geänderte Dateien | Art der Behebung | Commit |
|---|---|---|---|
| | | | |

# 9. Regressionstests

| Bereich | Test | Ergebnis | Beleg |
|---|---|---|---|
| Login/Session | | | |
| Rechte/Objekte | | | |
| REST-API | | | |
| MCP | | | |
| Agenten/Chat | | | |
| Wiki | | | |
| Upload/Download | | | |
| Integrationen | | | |
| Cronjob | | | |
| STT/TTS/Medien | | | |
| Jarvis | | | |

# 10. Fehlalarme

| ursprünglicher Hinweis | Begründung | Beleg |
|---|---|---|
| | | |

# 11. Offene und akzeptierte Risiken

| ID/Bereich | Risiko | Grund | Verantwortlicher | Zieltermin |
|---|---|---|---|---|
| | | | | |

# 12. Freigabeentscheidung

```text
[ ] keine offenen kritischen Befunde
[ ] keine ungeklärten hohen Befunde
[ ] alle Behebungen reproduzierbar getestet
[ ] Funktionsregression abgeschlossen
[ ] nicht prüfbare Bereiche dokumentiert
[ ] Restrisiken dokumentiert und entschieden
[ ] Bericht auf sensible Inhalte geprüft
[ ] menschliche Abnahme erfolgt

Entscheidung: freigegeben | nicht freigegeben | bedingt freigegeben
Begründung:
Name:
Datum:
```

# 13. Maschinenlesbare Befundliste

Mindestschema für `sicherheitsbefunde.json`:

```json
{
  "schema_version": "1.0",
  "projekt": "Noob2Claw",
  "commit": "",
  "befunde": [
    {
      "id": "N2C-SEC-001",
      "titel": "",
      "status": "offen",
      "schweregrad": "hoch",
      "vertrauensniveau": "hoch",
      "cwe": "",
      "owasp_asvs": "",
      "betroffene_dateien": [],
      "behebung_commit": null,
      "restrisiko": ""
    }
  ]
}
```
