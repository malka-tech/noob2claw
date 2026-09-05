# Noob2Claw – Folge 19: Sicherheitscheck der Webanwendung

In Folge 19 prüft ein OpenClaw- oder Hermes-Agent den vollständigen aktuellen
Noob2Claw-Code auf Sicherheitslücken. Bestätigte Befunde werden nachvollziehbar
dokumentiert, priorisiert, kontrolliert behoben und anschließend erneut getestet.

Die Prüfung verbindet automatische Werkzeuge mit manueller Datenfluss- und
Berechtigungsanalyse. Ein Scannerfund allein gilt nicht als bestätigte
Sicherheitslücke.

# Ziel der Folge

```text
vollständigen Scope erfassen
        ↓
Bedrohungsmodell erstellen
        ↓
automatische und manuelle Prüfungen
        ↓
Befunde reproduzieren und priorisieren
        ↓
Bericht vor der Behebung sichern
        ↓
Ursachen kontrolliert beheben
        ↓
Regression und erneuter Sicherheitscheck
        ↓
Abschlussbericht mit Restrisiken
```

# Sicherheitsgrenzen

- Zunächst wird ausschließlich lesend geprüft.
- Aktive Tests laufen nur gegen eine ausdrücklich freigegebene lokale oder
  isolierte Testinstanz.
- Keine Tests gegen Produktion, fremde Systeme oder externe Anbieter.
- Keine DoS-, Last-, Brute-Force-, Social-Engineering- oder Persistenztests.
- Keine echten Zugangsdaten oder personenbezogenen Inhalte in Prompts, Berichten,
  Terminalausgaben oder Git.
- Vorhandene lokale Änderungen bleiben erhalten.
- Destruktive Änderungen, Datenmigrationen und fachlich brechende Fixes benötigen
  eine ausdrückliche Freigabe.

# Enthaltene Dateien

- `0_Startprompt.md` – vollständiger ausführbarer Auftrag für OpenClaw oder Hermes
- `1_Sicherheitsaudit.md` – verbindlicher Prüf-, Behebungs- und Freigabeprozess
- `2_Berichtsvorlage.md` – einheitliche Vorlage für Auditberichte und Befunde

# Referenzrahmen

- OWASP Application Security Verification Standard 5.0.0:
  https://owasp.org/www-project-application-security-verification-standard/
- OWASP Web Security Testing Guide:
  https://wstg.owasp.org/
- Common Weakness Enumeration:
  https://cwe.mitre.org/

Die Referenzen unterstützen die Prüfung. Maßgeblich bleiben zusätzlich die
konkrete Noob2Claw-Architektur und die Anforderungen aus den Folgen 9 bis 18.

# Ergebnis

Nach Folge 19 existieren ein belegbarer Bericht vor der Behebung, ein Bericht
nach der Behebung und eine maschinenlesbare Befundliste. Kritische und hohe
Risiken sind entweder behoben oder ausdrücklich als nicht freigegeben markiert.

Eine Agentenprüfung ist keine Garantie vollständiger Sicherheit und ersetzt kein
unabhängiges professionelles Penetrationstest-Attest.

# Noob2Claw-Webseite

Weitere Informationen und Folgen: https://noob2claw.de/
