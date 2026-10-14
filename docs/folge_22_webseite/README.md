# Noob2Claw – Folge 22: Webseite

Folge 22 erweitert Noob2Claw um eine öffentliche, mehrseitenfähige Webseite mit
Backend-Verwaltung, SEO-Adressen, Templates und erweiterbaren PHP-Modulen.
Mehrere Webseiten teilen sich einen globalen Kundenstamm. Backend-Benutzer und
Kunden bleiben eigenständige Identitäten.

Die Bedienung folgt einem einfachen Ablauf: Webseite in drei Schritten anlegen,
Inhaltsvorlage wählen, Texte und Bilder im Blockeditor ändern, Vorschau prüfen
und veröffentlichen. Eine Domain kann später verbunden werden. Automatisches
Speichern sichert nur Entwürfe; öffentliche Inhalte ändern sich erst nach
gezielter Veröffentlichung.

## Verbindliche Ergebnisse

- Backend-Bereich `Webseiten` mit Webseiten, Domains, Webseitenbereichen,
  Inhaltsseiten, Navigation, Veröffentlichung und Templateauswahl.
- Globale Kundenverwaltung mit Registrierung, E-Mail-Verifikation,
  Kundenanmeldung und Passwortwiederherstellung.
- `/` liefert die öffentliche Startseite; `/index.php` und `/login.php` bleiben
  ausdrücklich Backend-Einstiege.
- Die Webseite bietet einen Backend-Login über den bestehenden Loginprozess.
- SEO-Adressen wie `/Impressum/` und `/meine-kategorie/`.
- Templates unter `/templates/XXX/` und registrierte PHP-Module mit klarer
  Trennung von Darstellung, Fachlogik und Datenzugriff.
- Erweiterungsverträge für spätere Shop-, Warenkorb- und Blogmodule.

## Enthaltene Dateien

- [0_Startprompt.md](0_Startprompt.md): Arbeitsauftrag für die Umsetzung.
- [1_Webseiten_und_Kunden.md](1_Webseiten_und_Kunden.md): Verwaltung,
  Datenmodell, Identitäten und Berechtigungen.
- [2_Routing_Templates_Module.md](2_Routing_Templates_Module.md): Routing,
  Templatevertrag, Modulvertrag und Erweiterungsbeispiele.
- [3_Umstellung_und_Abnahme.md](3_Umstellung_und_Abnahme.md): Reihenfolge,
  Umstellung, Rückweg und überprüfbare Abnahme.
- [4_Einfache_Bedienung_und_Redaktion.md](4_Einfache_Bedienung_und_Redaktion.md):
  verbindliche Bedienabläufe, Assistent, Editor, Medien und Veröffentlichung.

Die Folge implementiert die Plattform und ein Inhaltsmodul als Referenz.
Shop, Zahlung, Warenkorb und Blog werden noch nicht fachlich implementiert.
Hosting, DNS und TLS müssen für jede verwendete Domain eingerichtet sein;
das Speichern einer Domain im Backend erledigt diese Infrastrukturaufgaben nicht.
