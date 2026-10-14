# Noob2Claw – Folge 22: Webseitenverwaltung und globale Kunden

## 1. Verwaltung und Begriffe

Der Backend-Bereich `Webseiten` verwendet die bestehende automatisch erzeugte
Navigation und deren Metadaten. Im sichtbaren Webseitenkontext gibt es Übersicht,
Seiten, Design, Medien und Einstellungen. Bereiche und Menüs gehören zur
Seitenansicht; Domains und Module zu den Einstellungen. Die `Kundenverwaltung`
ist ein globaler Verwaltungsbereich mit eigenen Rechten. Dokument 4 definiert
die Bedienabläufe.

Eine Webseite besitzt eine stabile ID, Namen, Aktivstatus, primären Host,
verifizierte Host-Aliase, Sprache, Zeitzone, Template-Schlüssel und Einstellungen.
Im Entwurf darf der primäre Host fehlen; die geschützte Backendvorschau bleibt
möglich. Öffentliche Freischaltung benötigt eine bereite Domain.
Initial wird eine Webseite pro Host aufgelöst; dieselbe Installation bedient
mehrere Domains/Subdomains. Pfadbasierte Mandanten wie `/kunde-a/` sind nicht
Teil dieser Folge.

Ein Webseitenbereich ist eine redaktionelle Gruppe mit Schlüssel, Titel,
Beschreibung und Sortierung, beispielsweise `service` oder `magazin`.
Er kann eine eigene veröffentlichte Übersichtsseite `/meine-kategorie/` besitzen.
Eine Inhaltsseite gehört einer Webseite und optional einem ihrer Bereiche an.
Ein Bereich ist weder ein Dateipfad noch ein PHP-Modul oder Layout-Slot.

Layout-Slots wie `hauptinhalt`, `seitenleiste` und `fussbereich` werden vom Template
deklariert. In Slots platzierte Blöcke verweisen auf erlaubte Modulinstanzen.
Dadurch lassen sich redaktionelle Bereiche und visuelle Platzierung getrennt
verwalten.

## 2. Redaktionsablauf

Webseiten und Seiten werden zunächst als Entwurf angelegt. Eine Seite enthält
Titel, kanonischen Pfad, Inhaltsdaten, Bereich, Navigationseigenschaften,
SEO-Titel, Beschreibung, Indexierbarkeit und Veröffentlichungsstatus.
Ein Veröffentlichungsrecht ist von bloßem Bearbeiten getrennt.
Autosave ändert nur den Arbeitsentwurf. Veröffentlichte Revisionen bleiben
unveränderlich. Blöcke, SEO, Medienreferenzen und Pfad gehören zum Seitenstand;
Menüs, Design und gemeinsame Blöcke zum versionierten Webseitenstand.
Eine Veröffentlichung aktiviert eine geprüfte Zusammenstellung atomar.

Vorschauen benötigen einen autorisierten Backend-Benutzer oder einen kurzlebigen,
auf genau diese Revision und Webseite begrenzten Vorschautoken. Bei separaten
Domains darf kein Backend-Sessioncookie übertragen werden. Vorschauantworten
sind `no-store` und `noindex`; Tokens werden nicht protokolliert oder in
öffentlichen Links/Sitemaps ausgegeben. Formulare in Vorschauen sind deaktiviert.

Navigation speichert Seitenreferenzen und Reihenfolge, keine frei ausführbaren
Dateinamen. Externe Links werden auf zulässige Protokolle geprüft. Beim Löschen
oder Archivieren werden aktive Verweise und die Startseitenzuordnung geprüft.
Templatewechsel prüft vorher fehlende Slots und inkompatible Module; Inhalte
werden nicht still verworfen.

## 3. Datenmodell

Die folgenden Tabellen sind ein Zielmodell; bereits passende Strukturen werden
wiederverwendet. Migrationen sind additiv und wiederholt ausführbar.

| Struktur | Wesentliche Felder und Regeln |
|---|---|
| `webseiten` | ID, Name, Status, Sprache, Zeitzone, Arbeitsstand-ID, aktive Veröffentlichung-ID |
| `webseiten_konfigurationsrevisionen` | Webseite, unveränderlicher Stand von Template, Design, Startseite, Menüs, gemeinsamen Blöcken und Modulkonfiguration |
| `webseiten_veroeffentlichungen` | Webseite, Konfigurationsrevision, Seitenrevisions-Zuordnungen, Versionsnummer, Ersteller, Zeitpunkt |
| `webseiten_domains` | Webseite, normalisierter Host, primär/Alias, verifiziert; Host global eindeutig |
| `webseiten_bereiche` | Webseite, Schlüssel, Titel, Position, optionale Übersichtsseiten-ID |
| `webseiten_seiten` | Webseite, Bereich optional, Titel, Status, SEO-Daten, veröffentlichte Revision |
| `webseiten_revisionen` | Seite, Inhalt, SEO, Zielpfad, Medienreferenzen, Versionsnummer, Ersteller, Zeitpunkt |
| `webseiten_routen` | Webseite, normalisierter Pfadschlüssel, kanonischer Pfad, Zieltyp und Ziel-ID |
| `webseiten_weiterleitungen` | Webseite, alter Pfad, lokales Ziel, Statuscode; keine Schleifen |
| `webseiten_navigation` | Konfigurationsrevision, Navigationsplatz, Seitenreferenz oder geprüfter Link, Position |
| `webseiten_module` | Webseite, registrierter Modulschlüssel, Version, aktiv, validierte Konfiguration |
| `webseiten_bloecke` | eindeutige Seiten- oder Konfigurationsrevision, Slot, Modulreferenz, Position, validierte Inhaltsdaten |
| `webseiten_medien` | Referenz auf Dateiverwaltung, Webseite, private/öffentliche Freigabe, Varianten; neue Datei-ID bei Ersetzung |
| `kunden` | globale ID, normalisierte eindeutige E-Mail, Passwort-Hash, Status, verifiziert am |
| `kunden_webseiten` | Kunde, Webseite, Zuordnungsstatus, Registrierungsquelle; Paar eindeutig |
| `kunden_tokens` | Kunde, Zweck, Token-Hash, Webseite, Ablauf, verbraucht am |
| `kunden_sitzungen` | Kunde, Sitzungsreferenz-Hash, Hostkontext, Ablauf, widerrufen am; vorhandenen sicheren Speicher nutzen |

Alle redaktionellen Abfragen und Modulinstanzen sind an `webseite_id` gebunden.
Fremdschlüssel bzw. zentrale Transaktionsprüfungen verhindern die Zuordnung einer
Seite zu einem fremden Bereich. Routen sind je Webseite eindeutig und konkurrieren
mit Seiten, Bereichen, Modulen und Weiterleitungen in einem gemeinsamen Namensraum.
Öffentliche Routen und Weiterleitungen werden erst beim Veröffentlichen atomar
aktualisiert, gemeinsam mit dem aktiven Veröffentlichungsstand. Pfadkonflikte
werden dabei erneut geprüft. Livekonfiguration wird niemals aus veränderlichen
Arbeitskopien gelesen. Modulkonfigurationen gehören zur Veröffentlichungsrevision;
technische Notfall-Deaktivierung eines Moduls wirkt ausdrücklich sofort und
wird separat protokolliert. Revisionierung ersetzt kein Backup.

Das globale Kundenkonto enthält weder `backend_benutzer_id` als automatischen
Loginweg noch eine Backend-Rolle. Personen können unabhängig ein Kundenkonto und
ein Backend-Konto mit derselben E-Mail besitzen; es gibt keine automatische
Zusammenführung anhand dieser Adresse.

## 4. Registrierung und Kundensitzung

Die öffentliche Route `/registrieren/` nimmt validierte Registrierungsdaten und
eine CSRF-geschützte Aktion entgegen. Die globale E-Mail-Eindeutigkeit muss durch
einen Datenbankindex auch bei parallelen Registrierungen gelten. Passwörter
werden mit den bestehenden sicheren Hash-/Verifikationsfunktionen verarbeitet.

Ein neues Konto beginnt unverifiziert. Ein kryptografisch zufälliger, gehashter,
einmaliger und befristeter Token bestätigt die E-Mail. Tokenverbrauch erfolgt
atomar. Ohne funktionsfähigen Mailversand darf Verifikation nicht umgangen werden;
die Oberfläche bietet einen begrenzten erneuten Versand an.

Bei bereits registrierter Adresse liefert die Registrierung eine neutrale
Antwort. Sie ändert kein bestehendes Passwort und legt kein zweites Kundenkonto
an. Ein verifizierter Kunde kann sich auf einer weiteren Webseite anmelden und
nach Bestätigung der dort nötigen Bedingungen eine `kunden_webseiten`-Zuordnung
erhalten. Die Zuordnung dokumentiert den Zugang; sie vervielfacht das Konto nicht.

`/kunden-login/`, `/kundenkonto/` und `/passwort-vergessen/` betreffen ausschließlich
Kunden. Passwort-Reset verwendet eigene befristete Einmaltokens, ein neutrales
Antwortverhalten und Rate-Limits. Nach erfolgreichem Reset werden bestehende
Kundensitzungen widerrufen. Globale Sperrung wirkt auf alle Webseiten.

Backend- und Kundensitzung verwenden unterschiedliche Cookienamen, getrennten
Authentifizierungskontext, getrennte CSRF-Tokens und ausschließlich passende
Identitätsprüfungen. Sessions werden nach Anmeldung erneuert. Cookies sind unter
HTTPS `Secure`, `HttpOnly`, passend `SameSite` und hostgebunden. Die Konfiguration
erfolgt vor dem Sessionstart; kein Bootstrap darf zuvor ungeprüft eine
Backend-Sitzung für öffentliche Requests starten.

Global gilt für Zugangsdaten und Kundendatenbank, nicht für einen gemeinsamen
Cookie über fremde Domains. In Folge 22 meldet sich der Kunde pro Host an.
Domainübergreifendes SSO ist eine spätere Erweiterung mit eigenem Protokoll.
Kundenabmeldung löscht keine Backend-Sitzung und umgekehrt.

## 5. Backend-Anmeldung über die Webseite

Jedes Referenztemplate enthält „Backend anmelden“ mit dem Ziel `/login.php`.
Dadurch kann man von der Webseite aus den bestehenden Backend-Login erreichen.
Ein bereits angemeldeter Backend-Benutzer gelangt über `/index.php` ins Backend.
Nach Backend-Login wird auf einen erlaubten Backendpfad umgeleitet, nicht auf `/`.
Login-/Logout-/Navigationslinks des Bestands sind darauf zu prüfen.

Es entsteht kein zweiter Backend-Passwortprüfer im Template. Kundenpasswörter
werden niemals an die Backend-Authentifizierung weitergereicht. Ein eventuell
später eingebettetes Formular muss denselben Backend-Loginprozess einschließlich
CSRF, Rate-Limits, Sessionwechsel und vorhandener MFA verwenden.

## 6. Rechte und Datenschutz

Mindestens getrennte Rechte für Webseiten anzeigen/verwalten, Inhalte bearbeiten,
veröffentlichen, Vorschau, Domains verwalten, Module konfigurieren sowie Kunden
anzeigen/verwalten vorsehen. Codeinstallation ist keine Inhaltsberechtigung.
Die initiale Rechtevergabe folgt den bisherigen Administrator-Migrationen.

Backend-Rechte werden zusätzlich mit dem Zugriff auf die konkrete Webseite
geprüft. Globales Kundenlesen benötigt ein eigenes globales Recht; ein
webseitenbezogener Redakteur erhält damit keinen vollständigen Kundenexport.
Kunden sehen nur ihr eigenes Konto. Spätere Bestellungen/Warenkörbe prüfen
sowohl Kunden-ID als auch Webseiten-ID.

Nötige Einwilligungen, deren Versionen und Zwecke je Webseite dokumentieren;
Marketingfreigaben nicht aus globaler Registrierung ableiten. Geheimnisse,
Passwörter und Tokens erscheinen weder in Auditdaten noch in Reports. Ereignisse
gehen an den Sicherheitsdienst aus Folge 19. Aufbewahrung und Kontolöschung
werden zentral geregelt, ohne unbeabsichtigtes Löschen fremder Webseiteninhalte.
