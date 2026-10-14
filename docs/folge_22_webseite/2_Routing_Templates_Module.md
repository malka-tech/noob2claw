# Noob2Claw – Folge 22: Routing, Templates und PHP-Module

## 1. Verbindliche URL-Zuordnung

| Request | Zuständiger Einstieg und Verhalten |
|---|---|
| `/` | öffentliche Startseite der per Host bestimmten Webseite |
| `/index.php` samt bisherigen Queryparametern | bestehendes Backend mit dessen Authentifizierung |
| `/login.php` | bestehender Backend-Login |
| `/logout.php`, sofern vorhanden | bestehender Backend-Logout mit explizitem Ziel |
| `/api.php` | vorhandener zentraler API-Router, Authentifizierung je registrierter Aktion |
| `/mcp.php` | bestehender MCP-Router mit unveränderten Rechten |
| `/cron.php` über HTTP | ablehnen, bleibt ausschließlich CLI |
| `/Impressum/`, `/meine-kategorie/` | öffentliche Route der aktuellen Webseite |
| `/registrieren/`, `/kunden-login/`, `/kundenkonto/` | registrierte öffentliche Kundenrouten |
| `/sitemap.xml`, `/robots.txt` | webseitenbezogene öffentliche Ausgabe |
| unbekannter öffentlicher Pfad | echte HTTP-404, keine Backend-Anmeldeseite |

`webseite.php` wird als schlanker öffentlicher Frontcontroller ergänzt.
Die bestehende `index.php` bleibt Backend. Die Webserverzuordnung von `/` muss vor
dem bisherigen DirectoryIndex greifen: Sonst führt ein Aufruf von `/` intern
weiterhin zu `index.php`. Der Renderer entscheidet nicht anhand des Loginstatus,
ob ein Request Backend oder Webseite ist.

## 2. Routingreihenfolge und Domainauflösung

1. Infrastruktur prüft Host/SNI gegen die konfigurierten Hosts.
2. Interne Verzeichnisse, Konfigurationsdateien, Backups und CLI-Endpunkte sperren.
3. Exakte Backend-, API- und MCP-Einstiege sowie vorhandene legitime Endpunkte
   gemäß Bestandsinventar reservieren; PHP-PATH_INFO-Varianten nicht akzeptieren.
4. Nur ausdrücklich öffentliche statische Assets ausliefern.
5. `/` und öffentliche Pfade intern zu `webseite.php` leiten.
6. Dort Host validieren, aktive Webseite ermitteln und deren Route auflösen.

Kein pauschales „existierende Datei/Verzeichnis direkt ausliefern“, das private
Uploads, `inc/`, `sql/`, Logs oder Templates zugänglich macht. Verzeichnislisten
sind abgeschaltet. `/templates/XXX/assets/` ist der einzige direkt öffentliche
Teil eines Templates; PHP, Manifest und Partials bleiben von außen gesperrt.
Bestehende geschützte Downloads laufen weiter über deren Rechteprüfung.

Für Apache muss die tatsächliche VirtualHost-/Rewrite-Konfiguration inklusive
DirectoryIndex und interner Rewrite-Runden getestet werden. Falls das Zielsystem
Nginx nutzt, eine äquivalente Location-/FastCGI-Zuordnung liefern. Kein ungetesteter
pauschaler `.htaccess`-Ersatz: zuerst bestehende Regeln inventarisieren und in einer
Testinstanz die oben aufgeführte URL-Matrix nachweisen.

Der öffentliche Router liest den ursprünglichen Pfad, nicht einen vom Client
setzbaren Parameter wie `?webseite_id=2` oder `?backend=1`. Queryparameter dürfen
die Zuordnung zu Backend oder Webseite nicht überschreiben. Interner direkter
Zugriff auf `/webseite.php` wird kontrolliert abgelehnt oder für GET/HEAD auf den
kanonischen öffentlichen Pfad umgeleitet; keine zweite indexierbare Kopie.

Hosts werden zentral normalisiert und gegen `webseiten_domains` geprüft. Fremde
oder unbekannte Hosts erhalten eine kontrollierte Ablehnung; kein stiller Zugriff
auf die erste Webseite. Forwarded-Host/-Proto nur von konfigurierten
vertrauenswürdigen Proxys akzeptieren. Canonicals, Mail-Links und Redirects werden
aus der geprüften Domainkonfiguration erzeugt, nicht aus beliebigen Hostheadern.
Backendzugriff ist nur über explizit zugelassene Installationshosts möglich.
Verifizierte Webseitenhosts sind dafür standardmäßig freigegeben, sodass
`/index.php` und `/login.php` dort wie gefordert funktionieren. Eine zusätzliche
Verwaltungsdomain kann konfiguriert werden; Abweichungen von dieser Zuordnung
müssen ausdrücklich konfiguriert und im Template-Link berücksichtigt werden.
GET/HEAD auf verifizierten Aliasdomains führt zur primären Domain. Schreibende
Requests nur auf der vorgesehenen Domain annehmen, keine blinde Weiterleitung
von Login-, Registrierungs- oder Formulardaten zwischen Hosts.

Der Sicherheitsdienst aus Folge 19 prüft auch öffentliche Requests und
Kundenaktionen vor der Fachlogik. `api.php` erhält pro Aktion einen ausdrücklich
registrierten Kontext: anonym, Kunde oder Backend. Standard bleibt geschützt;
nur die konkret registrierten Anmelde-/Registrierungsaktionen sind anonym
zugänglich. Kundensitzungen dürfen nie Backendaktionen autorisieren.

## 3. SEO-Pfade

Seitenpfade sind Datenbankrouten, keine Dateinamen. `/Impressum/` darf als
kanonischer Pfad erhalten bleiben; der normalisierte Vergleichsschlüssel ist
case-insensitiv, damit `/impressum/` nicht eine zweite Seite bezeichnet.
Neue vorgeschlagene Slugs verwenden Kleinschreibung und Bindestriche.
Eine spätere Titeländerung ändert den bestehenden Pfad nicht. Gewünschte
Pfadänderungen werden als Entwurf gespeichert; Route und Weiterleitung wechseln
erst mit der Veröffentlichung gemeinsam auf den neuen Stand.

Pfadprüfung behandelt Prozentkodierung genau einmal, normalisiert Unicode
kontrolliert und weist fehlerhafte Kodierung, Nullbytes, Backslashes, Dot-Segmente
und kodierte Pfadtrenner zurück. Reservierte Präfixe und Endpunkte stehen für
redaktionelle Slugs nicht zur Verfügung. Regeln gelten auch für Modulrouten.

GET/HEAD-Varianten mit falscher Großschreibung oder fehlendem Endslash führen
mit 301 auf den gespeicherten kanonischen Pfad. Schreibende Requests werden
nicht per 301 umgebogen. Redirectketten und Kreise sind beim Speichern zu prüfen.
Pfadwechsel kann eine geprüfte lokale Weiterleitung vom alten Pfad anlegen.

Jede veröffentlichte Seite liefert genau einen Titel, Description und Canonical.
Sitemap enthält nur veröffentlichte indexierbare GET-Seiten des aktuellen Hosts.
Kundenkonto, Login, Reset und Vorschau sind nicht indexierbar. Robots-Anweisungen
ersetzen keine Zugriffskontrolle. Strukturierte Daten entstehen nur aus passenden
Inhalten; Metadaten werden kontextgerecht escaped.

## 4. Verzeichnis- und Templatevertrag

```text
/webseite.php
/templates/standard/
    template.json
    layout.php
    seite.php
    fehler_404.php
    teile/kopf.php
    teile/fuss.php
    assets/css/webseite.css
    assets/js/webseite.js
/inc/funktionen_webseiten.php
/inc/funktionen_webseiten_routing.php
/inc/funktionen_webseiten_templates.php
/inc/funktionen_webseiten_module.php
/inc/funktionen_kunden.php
/inc/webseiten_module/inhalt/
    modul.php
    ansichten/text.php
```

`XXX` in `/templates/XXX/` ist ein registrierter Schlüssel wie `standard`.
Das Template-Manifest enthält Schlüssel, Version, kompatible Plattformversion,
feste Ansichten, Slots und erlaubte Assetpfade. Schlüssel werden über Registry
und Verzeichnisgrenzen geprüft; keine Includes aus URL, Kundeneingabe oder
beliebigen Datenbankpfaden. Symlinks dürfen die erlaubten Grenzen nicht verlassen.

Das Manifest beschreibt zusätzlich sichtbare Feldnamen, Standardwerte, erlaubte
Farben-/Schriftvarianten, Blockfähigkeiten und Slot-Kompatibilität. Zwei
mitgelieferte Inhaltsvorlagen bestehen nur aus validierten Konfigurations- und
Blockdaten. Der Assistent erzeugt daraus Entwürfe mit neuen IDs und angepassten
internen Referenzen, ohne Domains, Kunden oder Geheimnisse zu kopieren.
Editor und Liveansicht verwenden dieselben Blockrenderer. Technische Slots werden
in der Bedienung als verständliche Positionen wie „Hauptinhalt“ bezeichnet.

Das Backend wählt installierte Templates aus und bearbeitet deren deklarierte
Einstellungen. Es bietet keinen beliebigen PHP-Editor und führt hochgeladenen
Code nicht aus. PHP-Templates sind vertrauenswürdiger Anwendungscode und keine
Sandbox für Kunden oder Redakteure.

Der Renderer übergibt ein festes Array `$ansicht` mit Webseite, Seite, Navigation,
Slots, freigegebenem Kundenanzeigestatus, URLs, CSRF-Formulardaten und Assets.
Keine Passwörter, DB-Verbindungen, Tokens oder vollständigen Kundenobjekte im
Templatekontext. Templates verwenden zentrale Escape-/URL-Helfer. Freitext ist
escaped; redaktionelles Rich-HTML wird serverseitig durch eine erlaubte
Element-/Attributliste bereinigt. Sichtbarkeit im Template ersetzt keine Rechte.

## 5. Vertrag für prozedurale PHP-Module

Eine zentrale Registry ordnet feste Schlüssel vertrauenswürdigen PHP-Dateien und
bekannten Funktionen zu. Ein Modul beschreibt als Array:

```text
schluessel, version, plattform_version
konfigurationsschema
routen (Pfadmuster, HTTP-Methoden, Auth-Kontext, feste Handler)
bloecke (feste Renderer, erlaubte Slots, Eingabeschema)
berechtigungen
migrationen
assets
optionale registrierte Wartungsaufgaben
```

Beispiel des Funktionsvertrags, keine neuen Klassen:

```php
webseiten_inhalt_definition();
webseiten_inhalt_daten($kontext, $parameter);
webseiten_inhalt_block($ansicht);
```

Definition liefert Metadaten, Datenfunktion liefert strukturierte validierte
Ansichtsdaten, Blockfunktion rendert die geprüfte Ansicht. Seitenhandler liefern
normierte Antworten mit Status, erlaubten Headern, Ansichtsdaten oder einem
geprüften Redirect. Layout und gemeinsame Header werden genau einmal vom
öffentlichen Renderer ausgegeben. Zustandsänderungen laufen über registrierte
Aktionen in `api.php` und zentrale Fachfunktionen.

Ein Requestkontext enthält serverseitig ermittelte Webseite, Sprache,
Kundenidentität oder Backendidentität als ausdrücklich unterschiedlichen Typ,
Request-ID und geprüfte Parameter. Die Webseite wird niemals aus einer frei
übergebenen Modulkonfiguration übernommen. Bei jedem Zugriff auf Seite,
Modulinstanz oder künftige Bestellung wird deren Webseite geprüft.

Modulinstallation und Webseitenaktivierung sind getrennt. Installation registriert
Code und versionierte Migrationen; Aktivierung prüft Kompatibilität, Abhängigkeiten,
Konfiguration und Routenkollisionen. Deaktivierung erhält gespeicherte Daten und
entfernt öffentliche Routen kontrolliert. Uninstall löscht keine Daten automatisch.
Ein Templatewechsel ändert keine Fachlogik oder Datenmodelle eines Moduls.
Der vorhandene globale Funktionsloader darf neue Moduldateien nicht automatisch
aktivieren oder beim Laden Requests, Migrationen oder Datenänderungen auslösen.
Registrierung und fachliche Ausführung sind getrennte Schritte.

Fehler erzeugen einen bereinigten Fehlerzustand und Korrelations-ID. Öffentliche
Requests sehen keine Stacktraces. Ein gestörter optionaler Block darf mit
Ersatzdarstellung ausfallen; zentrale Seiten-/Anmeldefehler liefern passende
Fehlercodes. Cache-Schlüssel enthalten Webseite, Sprache, Route, Inhaltsrevision
und Template-/Modulversion. Personalisierte Kontoseiten, CSRF und Vorschauen
dürfen nicht in einen gemeinsam genutzten öffentlichen Cache gelangen.
Die aktive Veröffentlichungs-ID gehört ebenfalls zum Cache-Schlüssel. Ein
Veröffentlichungswechsel schaltet alle referenzierten Stände zusammen um;
teilweise aktualisierte Menüs, Routes oder Styles dürfen nicht entstehen.
Template- und Modul-Codeversionen müssen für aktive Veröffentlichungen verfügbar
und kompatibel sein; Inhaltsrevisionen allein sind kein Code-Rollback.

## 6. Spätere Erweiterungen

| Modul | Verwendet | Eigene fachliche Daten und Routen |
|---|---|---|
| Blog | Bereiche, Seitenrenderer, Slots, SEO | Artikel und Kategorien je Webseite; `/blog/…/` |
| Shop | globales Kundenkonto, Webseitenkontext, Template | Produkte, Preise und Bestellungen je Webseite; `/shop/…/` |
| Warenkorb | Kundensitzung oder geprüfte Gastsitzung, Shopdienst | Warenkorb immer zusätzlich an Webseite gebunden; `/warenkorb/` |

Ein globaler Kunde führt nicht zu global geteilten Warenkörben oder Bestellungen.
Für spätere Bezahl-/Bestellmodule werden Methoden, Idempotenz und Transaktionen
explizit definiert. Diese Folge schafft nur Erweiterungspunkte und demonstriert
sie mit dem Inhaltsmodul; sie verspricht keine bereits vorhandene Shoplogik.

## Technische Referenzen

- [Apache DirectoryIndex](https://httpd.apache.org/docs/2.4/mod/mod_dir.html)
- [Apache Rewrite-Regeln](https://httpd.apache.org/docs/2.4/en/mod/mod_rewrite.html)
- [PHP Sitzungsnamen](https://www.php.net/manual/en/function.session-name.php)
- [PHP Sitzungskonfiguration](https://www.php.net/manual/en/session.configuration.php)
