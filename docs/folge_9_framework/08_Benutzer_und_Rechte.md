# 08_Benutzer_und_Rechte.md

# Benutzer und Rechte

Version: 1.0

Dieses Dokument definiert den Aufbau der Benutzerverwaltung, des Rollen- und Rechtekonzeptes sowie der Authentifizierung.

Die Benutzerverwaltung bildet die Grundlage für sämtliche Bereiche der Anwendung.

Alle Module müssen dieses Konzept verwenden.

---

# Ziel

Das Berechtigungssystem soll:

- einfach verständlich sein
- beliebig erweiterbar sein
- sicher sein
- zentral verwaltet werden
- für Weboberfläche, API und MCP identisch funktionieren

---

# Grundprinzip

Jeder Zugriff auf das System erfolgt über einen Benutzer.

Es existieren drei Benutzerarten:

- Web-Benutzer
- API-Benutzer
- MCP-Benutzer

Alle drei verwenden dieselbe Benutzerverwaltung sowie dasselbe Rollen- und Rechtekonzept.

Lediglich die Authentifizierung unterscheidet sich.

---

# Benutzerarten

## Web-Benutzer

Web-Benutzer melden sich über die Login-Seite an.

Authentifizierung:

- Benutzername oder E-Mail
- Passwort
- Session

---

## API-Benutzer

API-Benutzer dienen ausschließlich für REST-API-Zugriffe.

Authentifizierung:

- API-Key
- Bearer Token
- zukünftige OAuth-Unterstützung

API-Benutzer besitzen keinen Login über die Weboberfläche.

---

## MCP-Benutzer

MCP-Benutzer dienen ausschließlich für KI-Agenten und andere MCP-Clients.

Authentifizierung:

- MCP-Key
- Bearer Token

MCP-Benutzer besitzen keinen Login über die Weboberfläche.

---

# Gemeinsame Benutzerverwaltung

Alle Benutzer werden in derselben Benutzerverwaltung gepflegt.

Beispielsweise:

- Name
- E-Mail
- Beschreibung
- Status
- Rollen
- Berechtigungen

Zusätzlich besitzt jeder Benutzer einen Benutzertyp.

Beispiele

```text
web

api

mcp
```

Dadurch existiert nur eine zentrale Benutzerverwaltung.

---

# Aufbau

```text
Benutzer

↓

Rollen

↓

Rechte

↓

Anwendung
```

---

# Rollen

Eine Rolle fasst mehrere Berechtigungen zusammen.

Beispiele:

```text
Administrator

Entwickler

Support

Benutzer

Gast

API

MCP
```

Neue Rollen können jederzeit ergänzt werden.

---

# Rechte

Rechte beschreiben ausschließlich Fähigkeiten.

Beispiele:

```text
dashboard_anzeigen

agenten_anzeigen

agenten_starten

agenten_stoppen

benutzer_anzeigen

benutzer_bearbeiten

rollen_verwalten

logs_anzeigen

system_verwalten

api_agenten

api_system

mcp_tools

mcp_resources
```

Die Anwendung prüft ausschließlich Rechte.

Es wird niemals direkt geprüft, ob ein Benutzer Administrator oder Entwickler ist.

---

# Rollenzuordnung

Ein Benutzer kann mehrere Rollen besitzen.

Eine Rolle kann mehreren Benutzern zugeordnet werden.

---

# Rechtezuordnung

Eine Rolle besitzt mehrere Rechte.

Ein Recht kann mehreren Rollen zugeordnet werden.

---

# Anmeldung

## Web

Die Anmeldung erfolgt ausschließlich über

```text
login.php
```

Nach erfolgreicher Anmeldung wird eine Session erstellt.

---

## REST API

REST-Clients authentifizieren sich über

- API-Key
- Bearer Token

Es wird keine Web-Session verwendet.

---

## MCP

MCP-Clients authentifizieren sich über

- MCP-Key
- Bearer Token

Auch hier wird keine Web-Session verwendet.

---

# Trennung der Zugriffe

Die drei Zugriffsmöglichkeiten sind vollständig voneinander getrennt.

| Zugriff | Authentifizierung |
|----------|-------------------|
| Web | Benutzername + Passwort |
| API | API-Key / Token |
| MCP | MCP-Key / Token |

Ein erfolgreicher Web-Login berechtigt nicht automatisch zur Nutzung der REST-API.

Ebenso berechtigt ein API-Key nicht zur Nutzung des MCP-Servers.

Jeder Zugriff besitzt seine eigene Authentifizierung.

---

# Passwort

Passwörter werden niemals im Klartext gespeichert.

Es wird ausschließlich verwendet:

```php
password_hash()

password_verify()
```

---

# API-Schlüssel

API-Schlüssel werden niemals im Klartext gespeichert.

Sie werden ausschließlich als Hash gespeichert.

Beim Zugriff wird ausschließlich der Hash verglichen.

---

# MCP-Schlüssel

Auch MCP-Schlüssel werden ausschließlich gehasht gespeichert.

Sie dürfen niemals im Klartext in der Datenbank abgelegt werden.

---

# Session

Nach erfolgreicher Anmeldung wird eine Session erzeugt.

Die Session enthält beispielsweise:

- Benutzer-ID
- Benutzername
- Rollen
- Loginzeit

Es werden keine sensiblen Daten gespeichert.

---

# Rechteprüfung

Vor jeder Aktion erfolgt:

```text
Authentifizierung

↓

Benutzer aktiv

↓

Rechte vorhanden

↓

Aktion ausführen
```

Dies gilt gleichermaßen für:

- Web
- REST API
- MCP

---

# Rechte prüfen

Die Anwendung verwendet ausschließlich zentrale Funktionen.

Beispiel

```php
recht_pruefen("agenten_starten");
```

Nicht

```php
if ($rolle == "Administrator")
```

Dadurch bleibt das System flexibel.

---

# Sperrung

Benutzer können jederzeit gesperrt werden.

Gesperrte Benutzer können sich weder:

- im Web anmelden
- die API verwenden
- den MCP-Server nutzen

---

# Deaktivierung

Benutzer werden grundsätzlich nicht gelöscht.

Stattdessen werden sie deaktiviert.

Dadurch bleiben:

- Logs
- Historien
- Zuordnungen

erhalten.

---

# Passwortänderung

Passwörter dürfen ausschließlich über zentrale Funktionen geändert werden.

Direkte Datenbankupdates sind nicht zulässig.

---

# API- und MCP-Schlüssel

API- und MCP-Schlüssel können jederzeit:

- neu erzeugt
- deaktiviert
- gelöscht
- erneuert

werden.

Ein Schlüsseltausch darf keine Änderungen an Rollen oder Rechten erfordern.

---

# Loginversuche

Fehlgeschlagene Anmeldungen werden protokolliert.

Dies gilt sowohl für:

- Web
- API
- MCP

Optional können später ergänzt werden:

- Rate Limiting
- Captcha
- IP-Sperren
- automatische Sperrzeiten

---

# Logging

Folgende Ereignisse werden protokolliert:

- Login
- Logout
- API-Zugriff
- MCP-Zugriff
- Passwortänderung
- API-Key erstellt
- API-Key gelöscht
- MCP-Key erstellt
- MCP-Key gelöscht
- Benutzer angelegt
- Benutzer geändert
- Benutzer deaktiviert
- Rollen geändert
- Rechte geändert

---

# Erweiterbarkeit

Neue Rollen können jederzeit ergänzt werden.

Neue Rechte können jederzeit ergänzt werden.

Neue Authentifizierungsverfahren können jederzeit ergänzt werden.

Die bestehende Architektur muss hierfür nicht verändert werden.

---

# Verboten

Nicht erlaubt sind:

- Rollen direkt prüfen
- Passwörter im Klartext speichern
- API-Keys im Klartext speichern
- MCP-Keys im Klartext speichern
- Benutzer physisch löschen
- Rechte mehrfach implementieren
- Authentifizierung außerhalb der zentralen Funktionen
- SQL direkt auf Loginseiten

---

# Ziel

Die gesamte Anwendung verwendet eine zentrale Benutzerverwaltung mit einem gemeinsamen Rollen- und Rechtekonzept.

Weboberfläche, REST API und MCP verwenden dieselben Benutzer, Rollen und Berechtigungen.

Lediglich die Authentifizierung unterscheidet sich.

Dadurch entsteht ein einheitliches, sicheres und langfristig erweiterbares Berechtigungssystem.