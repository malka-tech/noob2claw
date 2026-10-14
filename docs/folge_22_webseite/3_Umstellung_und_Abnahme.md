# Noob2Claw – Folge 22: Umstellung und Abnahme

## 1. Vorbereitung

Dateien, Migrationen, Webserverkonfiguration und vorhandene Einstiegspunkte
inventarisieren. Backup und Wiederherstellung prüfen. Die Root-Adresse `/` ist
bisher möglicherweise Backendziel; Links nach Anmeldung, Abmeldung, Sessionablauf
und Navigation auf explizite Backendpfade anpassen.

Zwei Testwebseiten mit getrennten Hosts, Bereichen und Inhalten anlegen.
Ein Backendkonto und ein davon unabhängiges Kundenkonto mit gleicher E-Mail
nutzen. Kein Test gegen echte Kundendaten und keine ungefragte Produktivregistrierung.

## 2. Umstellungsfolge

1. Additive Migrationen, Registries und getrennte Auth-Kontexte bereitstellen.
2. Webseite, Bereiche, Testseiten und Standardtemplate im Backend konfigurieren.
3. Verifizierte Testhosts, Mailversand und Zertifikate prüfen.
4. Webseitenrenderer und Kundenabläufe unter Test-Webserverregeln testen.
5. Backend-, API-, MCP- und CLI-Regression ausführen.
6. Vorherige Webserverregeln sichern und Routing mit getesteter Rückfallmöglichkeit
   aktivieren. Konfigurationstest vor Reload durchführen.
7. Öffentliche Startseite, Backend-Login und zwei Hostkontexte erneut prüfen.

Rückweg: vorherige Webserverzuordnung wiederherstellen und neue öffentliche
Routen deaktivieren. Additive Tabellen erhalten; kein destruktiver Datenbankrollback
nach Kundenregistrierungen. Vorherige Anwendungsversion muss mit additiver
Migration kompatibel bleiben; andernfalls Rollout vorab überarbeiten.

## 3. Verbindliche Tests

| Prüfung | Erwartung |
|---|---|
| `/` anonym und als Backendbenutzer | jeweils öffentliche Startseite |
| `/index.php`, bisherige Backend-Queryparameter | Backend samt bisheriger Anmeldung/Rechte |
| `/login.php` und Link aus Webseite | bestehender Backend-Login, danach Backendziel |
| `/api.php`, `/mcp.php` | bisherige Verträge und Authentifizierung intakt |
| HTTP `/cron.php` | abgelehnt; CLI `cron.php alle` weiterhin funktionsfähig |
| `/Impressum/`, `/meine-kategorie/` | korrekte Webseite, Seite bzw. Bereichsübersicht |
| fehlender Slash, andere Schreibweise | eine kanonische GET-Adresse ohne Redirectkreis |
| unbekannter Pfad/Host | kontrollierte 404 bzw. Hostablehnung, keine fremden Inhalte |
| reservierte Slugs und Routenkollisionen | Speichern/Aktivieren verweigert |
| Traversal, doppelte Kodierung, PHP-PATH_INFO | keine Dateiausführung oder interne Dateiausgabe |
| Template-PHP, inc, sql, private Uploads per HTTP | nicht öffentlich lesbar/ausführbar |
| Template-Assets und bisherige Backend-Assets | nur freigegebene Dateien korrekt ausgeliefert |
| Entwurf, Vorschau, Sitemap | Entwurf nicht öffentlich; Vorschau geschützt; Sitemap hostbezogen |
| Webseiten A und B mit gleicher Route | Inhalte, Konfigurationen und Caches voneinander getrennt |
| globale Registrierung parallel mit gleicher E-Mail | genau ein Kundenkonto, keine Passwortüberschreibung |
| Verifikation/Reset abgelaufen oder wiederverwendet | abgelehnt, einmaliger atomarer Tokenverbrauch |
| Kunde meldet sich auf zweitem Host an | dieselbe globale Kunden-ID, getrennte Sitzung |
| Kundenlogin gegen Backendroute | keine Backend-Authentifizierung oder Rollenzuweisung |
| Backend- und Kundenlogout | nur jeweilige Sitzung beendet |
| CSRF, fremde Kunden-/Seiten-ID | serverseitig abgelehnt, auch bei direktem API-Aufruf |
| Templatewechsel, Modul deaktiviert | kontrollierter Zustand, keine still verlorenen Inhalte |
| präparierte Inhalte/Modulnamen | escaped/bereinigt, keine dynamischen Includes |
| Umlaute in Formularen, JSON und SEO-Pfaden | korrekte Konvertierung ohne beschädigte Zeichen |
| personalisierte Seiten/CSRF im Cache | keine Ausgabe an andere Kunden oder Webseiten |

Automatisierte Tests gezielt für Routing, Rechte, Tokenverbrauch, Hostauflösung
und kunden-/webseitenbezogene Datenabgrenzung erstellen. Bedienung zusätzlich
auf Desktop und Mobilgerät prüfen. Mailtests verwenden eine Test-Mailbox.

## 4. Abschlussartefakte

Zusätzlich wird der einfache Redaktionsablauf mit einer Person geprüft, die das
Modul noch nicht kennt. Ziel: innerhalb von etwa zehn Minuten ohne Code eine
Webseite aus Vorlage als Entwurf erstellen, Bild und Text ändern, eine zweite
Seite hinzufügen und die Mobilvorschau öffnen. DNS/TLS-Einrichtung zählt nicht
zu diesem Bedienziel. Zeit und konkrete Hindernisse dokumentieren; dies ist ein
Abnahmekriterium, keine bereits gemessene Produkteigenschaft.

Weitere Pflichtprüfungen:

- Anlage ohne Domain funktioniert, erneutes Absenden erzeugt keine Duplikate.
- Autosave, Bildersetzung, Menü- und Designänderung verändern die Liveansicht nicht.
- Seitenveröffentlichung übernimmt nur die ausgewählte Änderung; eine
  Webseitenveröffentlichung übernimmt die geprüften Stände atomar.
- Netzfehler zeigt keinen falschen Speichererfolg; Parallelbearbeitung erzeugt
  einen verständlichen Konflikt und erhält beide Fassungen.
- Alte Revision wiederherstellen erzeugt einen Entwurf; Live bleibt unverändert.
- Titel ändern behält die URL, Bereichswechsel erzeugt keine unbeabsichtigte URL-Änderung.
- Papierkorb, Wiederherstellung und referenzierte Bilder funktionieren ohne
  verlorene Inhalte oder still überschriebene URLs.
- Private Kunden-/Backenddateien sind in öffentlicher Medienauswahl ausgeschlossen.
- Webseitenwechsel, Tastaturbedienung und mobile Bearbeitung sind nachvollziehbar.
- Registrierungsabschaltung verhindert neue Beitritte, bestehende zugeordnete
  Kunden können sich weiterhin anmelden.

Abnahmebericht nennt geprüften Commit, Testumgebung, tatsächliche Webserverregeln,
Migrationen, Rechte, Template-/Modulbeispiele und Ergebnisse pro Prüffall.
Nicht ausgeführte Tests werden als solche gekennzeichnet. Abgeschlossen ist die
Implementierung erst, wenn zwei Webseiten und der globale separate Kundenstamm
funktionieren und das bestehende Backend weiterhin direkt erreichbar ist.
