# 15_Formular_Standards.md

# Formular-Standards

**Version:** 1.0  
**Projekt:** NoobClaw Webinterface  
**Gültigkeit:** Verbindlich für alle Formulare, Eingabemasken, Filter, Dialoge und Bearbeitungsseiten

---

# 1. Zweck dieses Dokuments

Dieses Dokument definiert den verbindlichen Aufbau und das Verhalten sämtlicher Formulare der Anwendung.

Es beschreibt:

- Formularstruktur,
- Feldtypen,
- Beschriftungen,
- Pflichtfelder,
- Validierung,
- Fehlerdarstellung,
- Speichern und Abbrechen,
- Sicherheitsregeln,
- barrierefreie Bedienung,
- responsive Darstellung,
- Lade- und Erfolgszustände,
- komplexe Formulare,
- Filterformulare,
- Formulare in Dialogen,
- Formulare für API-, MCP- und Agentenbereiche.

Dieses Dokument ergänzt:

```text
13_Design_und_UI_Standards.md
14_CSS_Standards.md
```

Tabellen werden separat beschrieben in:

```text
16_Tabellen_Standards.md
```

---

# 2. Ziel

Formulare sollen:

- sofort verständlich sein,
- schnell ausgefüllt werden können,
- Fehler früh erkennen,
- keine Daten verlieren,
- einheitlich aussehen,
- auf allen Geräten funktionieren,
- vollständig per Tastatur bedienbar sein,
- sichere Eingaben erzwingen,
- technische Details vor Benutzern verbergen.

Ein Formular ist erst fertig, wenn auch folgende Zustände definiert sind:

- leer,
- ausgefüllt,
- ungültig,
- wird gespeichert,
- erfolgreich gespeichert,
- Speichern fehlgeschlagen,
- keine Berechtigung,
- Datensatz wurde parallel geändert.

---

# 3. Grundprinzipien

## 3.1 Ein Formular besitzt genau ein Ziel

Ein Formular dient immer einer klaren Aufgabe.

Beispiele:

```text
Benutzer anlegen
Agent bearbeiten
API-Zugang erstellen
MCP-Berechtigungen ändern
Systemeinstellung speichern
```

Nicht erlaubt:

Ein einziges Formular, das gleichzeitig Benutzer, Agenten und Systemeinstellungen verändert.

---

## 3.2 Ein Formular besitzt genau eine Primäraktion

Beispiele:

```text
Speichern
Anlegen
Aktualisieren
Verbindung testen
Anmelden
```

Weitere Aktionen werden sekundär dargestellt.

---

## 3.3 Felder werden logisch gruppiert

Lange Formulare werden in Abschnitte gegliedert.

Beispiel:

```text
Allgemeine Informationen
Zugangsdaten
Berechtigungen
Sicherheit
Erweiterte Einstellungen
```

---

## 3.4 Pflichtfelder sparsam verwenden

Nur tatsächlich notwendige Felder werden als Pflichtfelder definiert.

Optionale Felder bleiben optional.

Ein Formular darf nicht künstlich mit Pflichtfeldern überladen werden.

---

## 3.5 Progressive Offenlegung

Seltene oder technische Optionen werden zunächst eingeklappt.

Beispiele:

```text
Erweiterte Einstellungen
Netzwerkbeschränkungen
Zusätzliche Header
Expertenmodus
```

---

# 4. Standardaufbau eines Formulars

```text
Seitentitel oder Dialogtitel
    ↓
Kurze Beschreibung
    ↓
Optionaler Hinweis
    ↓
Formularabschnitt 1
    ↓
Formularabschnitt 2
    ↓
Validierung / Fehlermeldungen
    ↓
Sekundäraktion
    ↓
Primäraktion
```

---

# 5. Formularbreite

Empfohlene maximale Breiten:

```text
Kompaktes Formular: 480px
Standardformular: 720px
Großes Formular: 960px
Mehrspaltiges Verwaltungsformular: 1200px
```

Ein Formular wird nicht unnötig über die gesamte Seitenbreite gezogen.

Lange Textfelder oder komplexe Konfigurationen dürfen mehr Breite erhalten.

---

# 6. Formularkopf

Jedes eigenständige Formular besitzt:

- Titel,
- kurze Beschreibung,
- optional Status,
- optional Warnhinweis,
- optional Kontext zum Datensatz.

Beispiel:

```text
API-Benutzer bearbeiten

Verwalte Zugangsdaten, Berechtigungen und IP-Beschränkungen.
```

---

# 7. Formularabschnitte

## 7.1 Aufbau

Jeder Abschnitt besitzt:

- Überschrift,
- optional kurze Beschreibung,
- Felder,
- optional Hilfetext.

Beispiel:

```text
Zugangsdaten

Diese Daten werden für die Anmeldung am zentralen API-Endpunkt verwendet.
```

---

## 7.2 Abstand

Zwischen Abschnitten:

```text
32px
```

Zwischen Feldern:

```text
16px
```

Zwischen Label und Feld:

```text
6px
```

---

# 8. Labels

## 8.1 Verbindliche Regel

Jedes Eingabefeld besitzt ein sichtbares Label.

Nicht erlaubt:

Nur ein Placeholder ohne Label.

Schlecht:

```html
<input placeholder="Benutzername">
```

Gut:

```html
<label for="benutzername">Benutzername</label>
<input id="benutzername" name="benutzername">
```

---

## 8.2 Labeltexte

Labels sind kurz und eindeutig.

Gut:

```text
Benutzername
E-Mail-Adresse
Beschreibung
IP-Adresse
Gültig bis
```

Schlecht:

```text
Bitte geben Sie hier den gewünschten Benutzernamen ein
```

Längere Erklärungen gehören in Hilfetexte.

---

# 9. Pflichtfelder

Pflichtfelder werden mit einem Stern gekennzeichnet:

```text
Benutzername *
```

Zusätzlich muss semantisch verwendet werden:

```html
required
aria-required="true"
```

Ein Hinweis am Formularanfang erklärt:

```text
Mit * markierte Felder sind Pflichtfelder.
```

---

# 10. Platzhalter

Placeholder dienen nur als Beispiel.

Sie ersetzen niemals das Label.

Beispiele:

```text
name@firma.de
192.168.1.20
api.meinefirma.de
```

Placeholder dürfen keine wichtigen Hinweise enthalten, da sie nach Eingabe verschwinden.

---

# 11. Hilfetexte

Hilfetexte stehen direkt unter dem Feld.

Beispiele:

```text
Der Benutzername muss innerhalb des Systems eindeutig sein.
```

```text
Mehrere IP-Adressen können durch Kommas getrennt werden.
```

```text
Der geheime Schlüssel wird nur einmal vollständig angezeigt.
```

Hilfetexte verwenden Sekundärtext und eine kleinere Schriftgröße.

---

# 12. Feldtypen

## 12.1 Textfeld

Für kurze, einzeilige Eingaben.

Beispiele:

```text
Benutzername
Hostname
Bezeichnung
E-Mail-Adresse
```

---

## 12.2 Textbereich

Für längere Inhalte.

Beispiele:

```text
Beschreibung
Notizen
Systemmeldung
Prompt
```

Textbereiche erhalten eine sinnvolle Mindesthöhe.

Sie dürfen vertikal vergrößert werden.

---

## 12.3 Passwortfeld

Passwortfelder besitzen:

- Maskierung,
- Schaltfläche zum Ein- und Ausblenden,
- Hinweis zu Anforderungen,
- keine automatische Vorbefüllung bei Bearbeitung,
- niemals Ausgabe des gespeicherten Hashes.

---

## 12.4 Zahlenfeld

Für echte numerische Werte.

Beispiele:

```text
Port
Priorität
Timeout
Rate Limit
```

Es werden sinnvolle Grenzen definiert:

```html
min
max
step
```

---

## 12.5 Datum und Zeit

Zeitwerte werden in der Datenbank als Unix-Timestamp gespeichert.

Die Benutzeroberfläche zeigt lokale Datums- und Zeitformate.

Es werden verwendet:

```text
Datum
Uhrzeit
Datum und Uhrzeit
```

Der Wert `0` bedeutet intern:

```text
nicht gesetzt
```

---

## 12.6 Auswahlfeld

Für eine einzelne Auswahl aus mehreren Werten.

Beispiele:

```text
Status
Protokoll
Benutzergruppe
Datentyp
```

Bei mehr als ungefähr zehn Optionen wird eine suchbare Auswahl bevorzugt.

---

## 12.7 Mehrfachauswahl

Für mehrere auswählbare Werte.

Beispiele:

```text
Benutzergruppen
MCP-Tools
API-Berechtigungen
Resources
```

Die Auswahl muss auch bei vielen Werten übersichtlich bleiben.

---

## 12.8 Checkbox

Für unabhängige Ja-/Nein-Optionen.

Beispiele:

```text
Aktiv
Logging einschalten
Benachrichtigungen senden
```

Checkboxen werden nicht für eine einzelne exklusive Auswahl verwendet.

---

## 12.9 Radiobuttons

Für eine exklusive Auswahl mit wenigen sichtbaren Optionen.

Beispiel:

```text
Theme:
( ) Hell
( ) Dunkel
( ) System
```

---

## 12.10 Schalter

Schalter werden nur verwendet, wenn eine Einstellung unmittelbar aktiviert oder deaktiviert wird.

Beispiele:

```text
Benutzer aktiv
MCP-Logging aktiv
Agent automatisch starten
```

Für bestätigungspflichtige oder gefährliche Aktionen wird kein Schalter verwendet.

---

## 12.11 Datei-Upload

Dateiuploads besitzen:

- sichtbaren Dateinamen,
- erlaubte Dateitypen,
- maximale Dateigröße,
- Uploadfortschritt,
- Entfernen-Aktion,
- Fehleranzeige,
- serverseitige Prüfung.

---

## 12.12 JSON-Eingabe

JSON-Felder werden nicht als normales kleines Textfeld dargestellt.

Sie erhalten:

- Monospace-Schrift,
- Syntaxhervorhebung, wenn verfügbar,
- Validierungsstatus,
- Formatieren-Aktion,
- verständliche Fehlermeldung,
- ausreichend Höhe.

---

# 13. Feldgrößen

Verbindliche Größen:

```text
Klein: 32px
Standard: 40px
Groß: 48px
```

Standardformulare verwenden:

```text
40px
```

Große Felder werden nur für prominente Einstiegsformulare verwendet.

---

# 14. Feldzustände

Jedes Feld benötigt folgende Zustände:

```text
Standard
Hover
Fokus
Ausgefüllt
Deaktiviert
Nur lesbar
Ungültig
Erfolgreich validiert
Lädt
```

---

# 15. Fokuszustand

Der Fokuszustand muss klar sichtbar sein.

Er verwendet:

- Primärfarbe,
- Fokusrahmen,
- ausreichenden Kontrast.

Der Fokus darf niemals vollständig entfernt werden.

---

# 16. Deaktiviert und nur lesbar

## Deaktiviert

Ein deaktiviertes Feld:

- ist nicht bearbeitbar,
- wird beim Absenden nicht übertragen,
- ist visuell klar deaktiviert.

## Nur lesbar

Ein Readonly-Feld:

- wird angezeigt,
- kann markiert und kopiert werden,
- wird bei Bedarf übertragen,
- ist nicht bearbeitbar.

Diese Zustände dürfen nicht verwechselt werden.

---

# 17. Validierung

## 17.1 Zweistufige Prüfung

Jede Eingabe wird geprüft:

```text
Clientseitig
Serverseitig
```

Die serverseitige Prüfung ist immer verbindlich.

JavaScript-Validierung ersetzt niemals die serverseitige Validierung.

---

## 17.2 Zeitpunkt der Validierung

Empfohlen:

- Pflichtfelder nach Verlassen des Feldes,
- Formatfehler nach sinnvoller Eingabe,
- vollständige Prüfung beim Absenden.

Nicht empfohlen:

Fehlermeldung bereits beim ersten Tastendruck.

---

## 17.3 Validierungsregeln

Beispiele:

```text
Pflichtfeld
Mindestlänge
Maximallänge
E-Mail-Format
Zahl
Wertebereich
IP-Adresse
URL
JSON
Eindeutigkeit
Berechtigung
Abhängigkeit zu anderem Feld
```

---

# 18. Fehlermeldungen

## 18.1 Feldfehler

Fehler stehen direkt unter dem betroffenen Feld.

Schlecht:

```text
Ungültige Eingabe
```

Gut:

```text
Der Benutzername muss mindestens 4 Zeichen enthalten.
```

---

## 18.2 Formularfehler

Zusätzlich wird am Formularanfang eine Zusammenfassung angezeigt, wenn mehrere Fehler bestehen.

Beispiel:

```text
Das Formular konnte nicht gespeichert werden.

Bitte prüfe die markierten Felder.
```

---

## 18.3 Technische Fehler

Technische Fehler werden nicht direkt angezeigt.

Schlecht:

```text
SQLSTATE[23000]: Integrity constraint violation
```

Gut:

```text
Der Benutzer konnte nicht gespeichert werden.
Bitte versuche es erneut.
```

Technische Details gehören in:

```text
db_fehler_log
system_log
```

---

# 19. Erfolgszustände

Nach erfolgreichem Speichern wird eindeutig bestätigt:

```text
Benutzer wurde gespeichert.
```

Mögliche Darstellung:

- Toast,
- Inline-Meldung,
- Weiterleitung mit Erfolgsmeldung.

Bei wichtigen Änderungen bleibt die Meldung sichtbar.

---

# 20. Speichern

## 20.1 Primärbutton

Der Speichern-Button steht:

- am Ende des Formulars,
- bei langen Formularen optional zusätzlich in einer fixierten Aktionsleiste.

---

## 20.2 Ladezustand

Während des Speicherns:

- Button wird deaktiviert,
- Spinner wird angezeigt,
- Text ändert sich optional zu `Wird gespeichert …`,
- Mehrfachabsenden wird verhindert.

---

## 20.3 Nach dem Speichern

Mögliche Verhaltensweisen:

```text
Speichern und auf Seite bleiben
Speichern und zur Liste zurück
Speichern und neuen Datensatz anlegen
```

Nur die häufigste Aktion wird primär dargestellt.

---

# 21. Abbrechen

Abbrechen darf keine Daten unbemerkt verlieren.

Wenn Änderungen vorhanden sind:

```text
Ungespeicherte Änderungen verwerfen?
```

Bei einfachen, noch leeren Formularen kann direkt abgebrochen werden.

---

# 22. Erkennung ungespeicherter Änderungen

Lange oder komplexe Formulare erkennen Änderungen.

Beim Verlassen der Seite wird gewarnt, wenn:

- Werte geändert wurden,
- Änderungen nicht gespeichert wurden.

Nicht warnen, wenn keine Änderung stattgefunden hat.

---

# 23. Optimistic Locking

Bei bearbeitbaren Datensätzen sollte eine Versions- oder Änderungsprüfung vorgesehen werden.

Beispiel:

```text
Der Datensatz wurde zwischenzeitlich von einem anderen Benutzer geändert.
```

Mögliche Optionen:

```text
Neu laden
Änderungen vergleichen
Trotzdem überschreiben
```

Blindes Überschreiben wird vermieden.

---

# 24. Formularlayout

## 24.1 Einspaltig

Standard für:

- Login,
- kompakte Einstellungen,
- Dialoge,
- mobile Geräte.

---

## 24.2 Zweispaltig

Geeignet für:

- Vorname / Nachname,
- Gültig ab / Gültig bis,
- Hostname / Port,
- Schlüssel-ID / Status.

---

## 24.3 Mehrspaltig

Nur für administrative Formulare mit klarer Struktur.

Mehr als drei Spalten werden vermieden.

---

# 25. Responsive Verhalten

## Mobil

- alle Felder einspaltig,
- Buttons möglichst volle Breite,
- große Touchflächen,
- keine horizontalen Formularlayouts,
- Dialoge nutzen fast die volle Breite.

## Tablet

- ein- oder zweispaltig,
- Aktionsleiste kann umbrechen.

## Desktop

- sinnvolle Zweispaltenlayouts,
- beschreibende Seitenleisten möglich,
- lange technische Felder dürfen breiter sein.

---

# 26. Tastaturbedienung

Die Tab-Reihenfolge folgt der visuellen Reihenfolge.

Unterstützt werden:

```text
Tab
Shift + Tab
Enter
Space
Escape
Pfeiltasten bei Auswahlkomponenten
```

Der Speichern-Button darf nicht unbeabsichtigt durch Enter ausgelöst werden, wenn ein mehrzeiliges Feld aktiv ist.

---

# 27. Autocomplete

Passende Werte werden gesetzt.

Beispiele:

```html
autocomplete="username"
autocomplete="current-password"
autocomplete="new-password"
autocomplete="email"
autocomplete="name"
```

Für API- und MCP-Schlüssel wird Autocomplete deaktiviert, wenn sinnvoll.

---

# 28. Sicherheit

## 28.1 CSRF-Schutz

Jedes verändernde Webformular benötigt einen CSRF-Schutz.

Der Token wird serverseitig geprüft.

---

## 28.2 XSS-Schutz

Ausgaben werden kontextgerecht escaped.

Benutzereingaben werden niemals ungefiltert als HTML ausgegeben.

---

## 28.3 SQL-Sicherheit

Formulare führen niemals direkt SQL aus.

Die Verarbeitung erfolgt ausschließlich über zentrale Funktionen.

---

## 28.4 Passwörter

- niemals im Klartext speichern,
- niemals im Log speichern,
- niemals erneut vollständig anzeigen,
- serverseitig mit `password_hash()` verarbeiten.

---

## 28.5 API- und MCP-Schlüssel

- nur einmal vollständig anzeigen,
- anschließend nur maskiert darstellen,
- nur Hash speichern,
- nie in Logs schreiben,
- Kopieren nur nach bewusster Benutzeraktion.

---

# 29. Loginformular

Das Loginformular enthält:

```text
Benutzername oder E-Mail
Passwort
Anmelden
Optional: Angemeldet bleiben
Optional: Passwort vergessen
```

Zusätzlich:

- klare Fehlermeldung,
- Rate Limiting,
- kein Hinweis, ob Benutzername oder Passwort falsch war,
- sicherer Sessionstart.

---

# 30. Benutzerformular

Empfohlene Abschnitte:

```text
Persönliche Daten
Zugangsdaten
Status
Benutzergruppen
Sicherheit
```

Felder:

```text
Benutzername
Vorname
Nachname
Anzeigename
E-Mail
Aktiv
Gesperrt
Gruppen
Passwort
```

---

# 31. API-Benutzerformular

Empfohlene Abschnitte:

```text
Allgemein
Zugangsdaten
Berechtigungen
IP-Beschränkungen
Gültigkeit
```

Felder:

```text
Benutzername
Bezeichnung
Beschreibung
Aktiv
Gültig ab
Gültig bis
Berechtigungen
Erlaubte IP-Adressen
```

Der geheime Schlüssel wird nach Erstellung in einem eigenen Bestätigungsdialog einmalig angezeigt.

---

# 32. MCP-Benutzerformular

Empfohlene Abschnitte:

```text
Allgemein
Zugangsdaten
Tools
Resources
Berechtigungen
IP-Beschränkungen
Gültigkeit
```

Felder:

```text
Benutzername
Bezeichnung
Beschreibung
Aktiv
Tools
Resources
Berechtigungen
Erlaubte IP-Adressen
Gültig ab
Gültig bis
```

---

# 33. Agentenformular

Empfohlene Abschnitte:

```text
Allgemein
Zuordnung
Verbindung
Status
Konfiguration
```

Felder:

```text
Typ
Haupt-Agent
Bezeichnung
Beschreibung
Hostname
IP-Adresse
Port
Protokoll
Basis-URL
Aktiv
Konfiguration
```

## Haupt-Agent

Für einen Agent-Server:

```text
haupt_id = 0
```

Für einen Sub-Agenten:

```text
haupt_id = ID des Agent-Servers
```

Die Oberfläche zeigt diese Beziehung verständlich als Auswahlfeld.

---

# 34. Einstellungen

Einstellungen werden nicht als unstrukturierte Liste dargestellt.

Sie werden gruppiert nach:

```text
System
Design
Dashboard
API
MCP
Agenten
Sicherheit
```

Die Speicherung erfolgt über:

```text
funktionen_einstellungen.php
```

Direkte SQL-Zugriffe sind nicht erlaubt.

---

# 35. Filterformulare

Filterformulare verändern keine Daten.

Sie bestehen aus:

```text
Suche
Status
Zeitraum
Bereich
Benutzer
Zurücksetzen
Anwenden
```

Häufige Filter können sofort reagieren.

Komplexe Filter benötigen einen Anwenden-Button.

---

# 36. Formulare in Dialogen

Dialogformulare sind nur für kurze Eingaben geeignet.

Geeignet:

```text
Bezeichnung ändern
Benutzergruppe zuordnen
Session beenden
Agent stoppen
```

Nicht geeignet:

```text
Kompletten Agenten konfigurieren
Komplexe MCP-Berechtigungen bearbeiten
Lange Systemkonfiguration
```

---

# 37. Bestätigungsdialoge

Kritische Aktionen zeigen:

- betroffenen Datensatz,
- Konsequenz,
- Warnung,
- eindeutige Bestätigung.

Beispiel:

```text
Agent stoppen?

Alle laufenden Aufgaben dieses Agenten werden unterbrochen.
```

---

# 38. Löschdialoge

Bei endgültigen Löschungen kann eine Texteingabe verlangt werden.

Beispiel:

```text
Zum Löschen „LÖSCHEN“ eingeben.
```

Wo möglich, wird Soft Delete oder Deaktivierung bevorzugt.

---

# 39. Mehrstufige Formulare

Mehrstufige Formulare werden nur verwendet, wenn die Eingabe logisch in Schritte zerfällt.

Beispiel:

```text
1. Allgemein
2. Verbindung
3. Berechtigungen
4. Prüfung
```

Regeln:

- aktueller Schritt sichtbar,
- zurück möglich,
- Zwischenspeicherung bei langen Prozessen,
- abschließende Zusammenfassung.

---

# 40. Inline-Bearbeitung

Inline-Bearbeitung wird sparsam verwendet.

Geeignet:

```text
Bezeichnung
Priorität
Aktivstatus
```

Nicht geeignet:

```text
Passwörter
Berechtigungen
komplexe JSON-Konfiguration
```

---

# 41. Automatisches Speichern

Autosave wird nur dort verwendet, wo Datenverlust unwahrscheinlich ist.

Geeignet:

```text
Dashboard-Anordnung
UI-Einstellungen
eingeklappte Navigation
```

Nicht geeignet:

```text
Benutzerrechte
API-Schlüssel
MCP-Zugänge
kritische Agentenkonfiguration
```

Autosave benötigt einen sichtbaren Status:

```text
Wird gespeichert …
Gespeichert
Fehler beim Speichern
```

---

# 42. Datentypen und Eingabeformate

## Boolean

Darstellung:

```text
Schalter oder Checkbox
```

Speicherung:

```text
0 oder 1
```

## Integer

Nur ganze Zahlen zulassen.

## Decimal

Korrekte Dezimaltrennung in der Oberfläche beachten.

## JSON

Vor Speicherung validieren.

## Unix-Timestamp

In der Oberfläche lokal formatiert anzeigen.

---

# 43. Keine NULL-Werte

Die Formulare müssen die Datenbankregel berücksichtigen:

```text
NULL wird nicht verwendet.
```

Standardwerte:

```text
ID: 0
Integer: 0
Decimal: 0.00
Text: ''
JSON-Objekt: {}
JSON-Liste: []
Zeit: 0
```

Leere optionale Felder werden serverseitig in den passenden Standardwert umgewandelt.

---

# 44. Serverseitige Verarbeitung

Verbindlicher Ablauf:

```text
Request prüfen
    ↓
CSRF prüfen
    ↓
Berechtigung prüfen
    ↓
Eingaben normalisieren
    ↓
Eingaben validieren
    ↓
Business-Funktion aufrufen
    ↓
Ergebnis protokollieren
    ↓
Antwort erzeugen
```

---

# 45. Rückmeldungsformat

Business-Funktionen sollen strukturierte Ergebnisse liefern.

Beispiel:

```php
[
    'erfolg' => true,
    'meldung' => 'Benutzer wurde gespeichert.',
    'daten' => [
        'id' => 15,
    ],
    'fehler' => [],
]
```

Fehler:

```php
[
    'erfolg' => false,
    'meldung' => 'Das Formular enthält Fehler.',
    'daten' => [],
    'fehler' => [
        'benutzername' => 'Der Benutzername ist bereits vergeben.',
        'email' => 'Die E-Mail-Adresse ist ungültig.',
    ],
]
```

---

# 46. Namenskonventionen

Feldnamen orientieren sich an den Datenbankspalten.

Beispiele:

```text
benutzername
vorname
nachname
haupt_id
ip_adresse
gueltig_bis
```

Keine abweichenden Fantasienamen zwischen Formular und Datenbank ohne technischen Grund.

---

# 47. HTML-Grundstruktur

Beispiel:

```html
<form class="formular" method="post" novalidate>
    <input
        type="hidden"
        name="csrf_token"
        value="<?= html_escape($csrf_token); ?>"
    >

    <section class="formular-abschnitt">
        <header class="formular-abschnitt__kopf">
            <h2 class="formular-abschnitt__titel">
                Allgemeine Informationen
            </h2>

            <p class="formular-abschnitt__beschreibung">
                Grundlegende Daten des Benutzers.
            </p>
        </header>

        <div class="formular-raster">
            <div class="formular-feld">
                <label
                    class="formular-feld__label"
                    for="benutzername"
                >
                    Benutzername
                    <span aria-hidden="true">*</span>
                </label>

                <input
                    class="eingabe"
                    id="benutzername"
                    name="benutzername"
                    type="text"
                    required
                    aria-required="true"
                    autocomplete="username"
                >

                <p class="formular-feld__hilfe">
                    Muss innerhalb des Systems eindeutig sein.
                </p>
            </div>
        </div>
    </section>

    <footer class="formular-aktionen">
        <a class="button button--sekundaer" href="index.php">
            Abbrechen
        </a>

        <button class="button button--primaer" type="submit">
            Speichern
        </button>
    </footer>
</form>
```

---

# 48. CSS-Klassen

Vorgesehene Klassen:

```text
.formular
.formular-abschnitt
.formular-abschnitt__kopf
.formular-abschnitt__titel
.formular-abschnitt__beschreibung
.formular-raster
.formular-feld
.formular-feld__label
.formular-feld__hilfe
.formular-feld__fehler
.formular-aktionen

.eingabe
.eingabe--fehler
.eingabe--erfolg
.eingabe--kompakt
```

---

# 49. JavaScript-Regeln

JavaScript darf:

- Komfortfunktionen bereitstellen,
- Felder dynamisch ein- und ausblenden,
- clientseitig validieren,
- JSON formatieren,
- Uploadfortschritt anzeigen,
- Abhängigkeiten zwischen Feldern steuern.

JavaScript darf nicht:

- alleinige Sicherheitsprüfung durchführen,
- Berechtigungen ersetzen,
- Daten ungeprüft speichern,
- serverseitige Validierung ersetzen.

---

# 50. Logging

Folgende Formularaktionen werden bei Bedarf protokolliert:

```text
Datensatz angelegt
Datensatz geändert
Datensatz deaktiviert
Berechtigungen geändert
Passwort geändert
API-Key erzeugt
MCP-Key erzeugt
Agentenkonfiguration geändert
```

Geheimnisse werden niemals protokolliert.

---

# 51. Qualitätsprüfung

Ein Formular gilt erst als fertig, wenn folgende Punkte geprüft wurden:

```text
[ ] Formular besitzt ein klares Ziel
[ ] Genau eine Primäraktion
[ ] Alle Felder besitzen Labels
[ ] Pflichtfelder sind gekennzeichnet
[ ] Hilfetexte sind verständlich
[ ] Clientvalidierung vorhanden
[ ] Servervalidierung vorhanden
[ ] Feldfehler werden direkt angezeigt
[ ] Technische Fehler werden verborgen
[ ] CSRF-Schutz vorhanden
[ ] Rechteprüfung vorhanden
[ ] Keine direkten SQL-Zugriffe
[ ] Keine Geheimnisse im Log
[ ] Ladezustand vorhanden
[ ] Erfolgszustand vorhanden
[ ] Fehlerzustand vorhanden
[ ] Ungespeicherte Änderungen berücksichtigt
[ ] Tastaturbedienung geprüft
[ ] Mobil geprüft
[ ] Tablet geprüft
[ ] Desktop geprüft
[ ] Light Mode geprüft
[ ] Dark Mode geprüft
[ ] Keine NULL-Werte erzeugt
```

---

# 52. Verbotene Muster

Nicht erlaubt:

```text
Placeholder statt Label
Mehrere Primärbuttons
Direktes SQL im Formular
Nur clientseitige Validierung
Technische Fehlermeldungen im Frontend
Passwörter im Klartext
API- oder MCP-Schlüssel im Log
NULL als leerer Wert
Unklare Fehlermeldungen
Automatisches Speichern kritischer Rechte
Lange komplexe Formulare in kleinen Dialogen
Versteckte Pflichtfelder
Speichern ohne Ladezustand
Unbemerkter Datenverlust beim Abbrechen
```

---

# 53. Zusammenfassung

Alle Formulare der Anwendung folgen einem einheitlichen System.

Verbindliche Regeln:

- klare Aufgabe,
- sichtbare Labels,
- sparsame Pflichtfelder,
- logische Abschnitte,
- genau eine Primäraktion,
- client- und serverseitige Validierung,
- verständliche Fehlermeldungen,
- CSRF- und Rechteprüfung,
- keine direkten SQL-Zugriffe,
- keine Speicherung von Geheimnissen,
- responsive Darstellung,
- vollständige Tastaturbedienung,
- Berücksichtigung der Datenbankregel ohne `NULL`,
- strukturierte Rückgaben aus Business-Funktionen.

Neue Formulare erweitern das bestehende System.

Sie dürfen keine abweichende Formularlogik oder eigene Designsprache einführen.