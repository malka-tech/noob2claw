# Noob2Claw – Folge 22: Einfach Webseiten erstellen und pflegen

Dieses Bedienkonzept ist verbindlicher Teil der Umsetzung. Der normale Ablauf
lautet: Webseite anlegen, Vorlage wählen, Inhalte ändern, Vorschau ansehen und
veröffentlichen. Technische Einstellungen liegen unter „Einstellungen“ und
„Erweitert“; sie sind für die tägliche Inhaltspflege nicht erforderlich.

## 1. Übersicht und eindeutiger Webseitenkontext

Die Übersicht zeigt je Webseite Name, Vorschaubild, Domain oder „Noch keine Domain“,
Veröffentlichungsstatus und letzte Bearbeitung. Hauptaktionen sind „Bearbeiten“,
„Vorschau“ und „Webseite hinzufügen“. Suche und Statusfilter ergänzen die Liste.
Eine leere Übersicht erklärt den nächsten Schritt direkt neben dem Anlegen-Button.

Nach Auswahl bleibt die aktuelle Webseite mit Name und Domain sichtbar. Ein
Webseitenwechsler erlaubt den Wechsel ohne Rückkehr zur Startseite. Nicht
gespeicherte Änderungen müssen vor dem Wechsel gesichert oder bewusst verworfen
werden. Kunden bleiben als ausdrücklich globaler Bereich getrennt sichtbar.

Innerhalb einer Webseite reichen diese Navigationseinträge:

- **Übersicht:** nächster Einrichtungsschritt, letzte Änderungen und Vorschau.
- **Seiten:** Seitenliste, Bereiche und Menüs in einer gemeinsamen Arbeitsansicht.
- **Design:** Vorlage, Logo, Farben, Schriftwahl und Fußbereich.
- **Medien:** verwendbare Bilder und Downloads mit Suche und Verwendungsnachweis.
- **Einstellungen:** Domain, Kundenzugang und erweiterte Modulkonfiguration.

Interne Tabellen, Modulschlüssel und Slotnamen werden nicht als zusätzliche
Pflichtmenüs dargestellt. Installationen von PHP-Code bleiben eine technische
Verwaltungsfunktion. Berechtigungsabhängige Aktionen sind verständlich benannt.

## 2. Webseite in drei Schritten anlegen

1. **Name und Vorlage:** Name eingeben; eine mitgelieferte Startvorlage auswählen.
   Sprache und Zeitzone sind sinnvoll vorbelegt und optional änderbar.
2. **Aussehen und Inhalte:** Logo optional wählen, Hauptfarbe setzen und eine
   vorbereitete Startseite mit eigenen Texten und Bildern füllen.
3. **Prüfen und starten:** Vorschau öffnen, Domain jetzt verbinden oder später
   ergänzen. „Als Entwurf speichern“ bleibt jederzeit möglich.

Die Anlage muss bereits ohne Domain und ohne Mailkonfiguration funktionieren.
Eine geschützte Vorschau auf dem Verwaltungsserver zeigt den Entwurf. Öffentliche
Freischaltung benötigt eine funktionsfähige Domain; Registrierung benötigt
zusätzlich geprüften Mailversand. Beides blockiert nicht die Erstellung von Inhalten.

Mindestens zwei Inhaltsvorlagen werden mit dem Standardtemplate ausgeliefert:
„Kompakte Webseite“ und „Unternehmen“. Sie kombinieren dieselben geprüften Blöcke
unterschiedlich und benötigen keine getrennten PHP-Templates. Namen und Beispiele
sind sichtbar als Platzhalter gekennzeichnet. Rechtstexte werden nicht automatisch
als angeblich fertige oder rechtssichere Inhalte veröffentlicht.

Wiederholtes Absenden oder Wiederaufnahme nach einem Fehler darf weder doppelte
Webseiten noch doppelte Startseiten erzeugen. Die Anlage speichert den Entwurf
transaktional und verwendet einen eindeutigen Anlegevorgang.

## 3. Seiteneditor mit Vorschau

Die Seitenansicht hat links Seiten und Bereiche, in der Mitte die Inhaltsvorschau
und rechts die Einstellungen des ausgewählten Blocks. Auf kleinen Bildschirmen
werden diese Bereiche als umschaltbare Ansichten dargestellt. Die wichtigsten
Aktionen bleiben erreichbar: „Vorschau“, „Speichern“ und „Veröffentlichen“.

„Seite hinzufügen“ benötigt zunächst nur Titel und eine Inhaltsvorlage oder
„Leere Seite“. Der URL-Vorschlag entsteht automatisch. Die Option „Im Menü
anzeigen“ ist vorbelegt, die Position anschließend änderbar. SEO-Felder und
technische Optionen liegen in aufklappbaren Zusatzbereichen.

Der Editor bietet eine begrenzte Auswahl fachlich klarer Inhaltsblöcke:

- Titelbereich mit Überschrift, Text, Bild und optionalem Linkbutton,
- Text mit eingeschränkter Rich-Text-Formatierung,
- Bild mit Alternativtext und optionaler Bildunterschrift,
- Text mit Bild,
- Linkbutton,
- Linkkarten zu anderen veröffentlichten Seiten,
- Abstand oder Trennlinie.

Blöcke lassen sich hinzufügen, duplizieren, verschieben und entfernen. Zusätzlich
zu Drag-and-drop gibt es beschriftete Schaltflächen und Tastaturbedienung. Der
Editor zeigt nur erlaubte Varianten wie Ausrichtung und Breite. Freies CSS,
HTML und PHP sind kein notwendiger Teil der Inhaltserstellung.

Die Vorschau verwendet dieselben Renderer wie die öffentliche Ausgabe. Vorschauen
für Desktop und Mobilgerät sind direkt umschaltbar. Eine Sandbox bzw. gleichwertige
Isolation verhindert den Zugriff von Vorschauinhalten auf die Backendoberfläche.
Formularaktionen bleiben dort deaktiviert. Ist die Ziel-Domain noch nicht bereit,
erfolgt die Vorschau über eine geschützte Backendaktion mit explizitem Webseiten-
und Revisionskontext; der öffentliche Hostrouter erhält keinen beliebigen
Webseiten-ID-Override.

## 4. Automatisch speichern und gezielt veröffentlichen

Automatisches Speichern sichert nur den Arbeitsentwurf nach kurzer Eingabepause.
Der sichtbare Status lautet „Speichert …“, „Gespeichert um …“ oder „Speichern
fehlgeschlagen“. Ein expliziter Speichern-Button bleibt vorhanden. Erfolg darf
erst nach Serverbestätigung angezeigt werden. Bei Netzfehlern bleiben Eingaben
im geöffneten Editor erhalten; Navigation warnt vor Datenverlust.

Jeder Schreibzugriff enthält die erwartete Version. Bei konkurrierender Änderung
meldet der Server einen Konflikt statt die letzte Fassung still zu überschreiben.
Der Editor erlaubt den Vergleich mit dem Serverstand und das Sichern der eigenen
Fassung als separaten Entwurf. Rechte werden auch bei Autosave geprüft.

„Veröffentlichen“ zeigt kompakt, welche Inhalte und URLs geändert werden. Nach
Bestätigung mit Veröffentlichungsrecht wird exakt der geprüfte Entwurfsstand
freigegeben. Änderungen im Entwurf werden zuvor nicht live sichtbar. Während
einer Bearbeitung bleibt die letzte veröffentlichte Fassung erreichbar.

Eine Veröffentlichung umfasst Inhaltsrevision, Blöcke, Metadaten und Route
atomar. Menüs, Design und gemeinsame Fußbereiche besitzen eigene Arbeits- und
veröffentlichte Stände; bei „Webseite veröffentlichen“ werden die ausgewählten
Stände gemeinsam in einer unveränderlichen Veröffentlichung gebündelt.
Eine normale Seitenveröffentlichung nimmt keine fremden offenen Design- oder
Menüänderungen mit. Cache, Sitemap und Navigation beziehen sich auf denselben
veröffentlichten Stand.

Versionsverlauf zeigt Datum, Bearbeiter und Änderung. „Wiederherstellen“ erzeugt
einen neuen Entwurf aus einer älteren Revision. Erst erneutes Veröffentlichen
macht ihn öffentlich. Löschen legt Inhalte zunächst in den Papierkorb; Referenzen
und Startseite werden geprüft. Wiederherstellung löst URL-Konflikte sichtbar.
Autosaves werden zusammengefasst und begrenzt aufbewahrt; aktive veröffentlichte
und referenzierte Revisionen dürfen dabei nicht gelöscht werden.

## 5. Bereiche, Menüs und Links

Bereiche gruppieren Seiten. „Bereich hinzufügen“ benötigt nur einen Namen;
eine öffentliche Übersichtsseite ist optional. Verschieben in einen Bereich
ändert die bestehende Seitenadresse nicht automatisch.

Menüs werden visuell aus Seitenreferenzen aufgebaut, mit Hauptmenü und Fußmenü
als Voreinstellung. Einträge lassen sich sortieren und begrenzt verschachteln.
Redakteure müssen interne URLs nicht abtippen: Ein Seitenauswahldialog liefert
eine stabile Referenz. Linkziele werden beim Rendern aus veröffentlichten Routen
ermittelt. Umbenennen einer Seite ändert ihre bestehende URL nicht automatisch.
Bei bewusster URL-Änderung wird eine lokale Weiterleitung vorgeschlagen.

Entwürfe erscheinen nicht als kaputte öffentliche Menülinks. Vor einer
Veröffentlichung werden fehlende Ziele geprüft und konkrete Korrekturen angeboten.
Globale Kopf-/Fußinhalte und Logo werden einmal je Webseite gepflegt.

## 6. Medien und Design

Die Medienansicht erweitert die vorhandene Dateiverwaltung. Sie bietet Upload,
Suche, Vorschau, Alternativtext und Verwendungsnachweis. Größen- und Formatfehler
werden direkt erklärt. Bilder erhalten geeignete Anzeigevarianten und feste
Abmessungen; bestehende sichere Bildverarbeitung wiederverwenden.

Medien besitzen eine explizite Webseitenzuordnung und Sichtbarkeit. Neue Uploads
sind zunächst privat. Erst eine Veröffentlichung darf freigegebene Medien
öffentlich bereitstellen. Private Kunden- oder Backenddateien dürfen nicht über
die Medienauswahl öffentlich werden. Wird ein benutztes Bild ersetzt, bleiben
alte Referenzen bis zur Veröffentlichung gültig. Löschen benutzter Medien wird
mit den betroffenen Seiten angezeigt und verhindert oder bewusst aufgelöst.

Design bietet Logo, wenige Farbfelder, lokal verfügbare Schriftvarianten und
vordefinierte Layoutvarianten. Das Referenztemplate besitzt responsive, mit
Tastatur bedienbare Komponenten und erkennbare Fokuszustände. Eingaben sind durch
das Templateschema begrenzt; Browser laden nicht ungefragt fremde Schriftanbieter.

## 7. Veröffentlichung und verständliche Betriebszustände

Die Übersicht unterscheidet „Entwurf“, „Online“, „Online mit unveröffentlichten
Änderungen“ und „Offline“. Domainzustände sind separat: „Nicht eingerichtet“,
„Prüfung ausstehend“, „Bereit“ oder „Fehler“. Ein grüner Domainstatus setzt
nachgewiesene Zuordnung, Erreichbarkeit und TLS voraus, nicht bloß einen DB-Eintrag.

Die Veröffentlichung nennt echte Blocker mit direkter Aktion, etwa „Startseite
auswählen“, „Doppelte Adresse ändern“ oder „Domain verbinden“. SEO-Beschreibung
oder optionale Bilder sind Hinweise, keine pauschalen Veröffentlichungssperren.
Domainprüfungen dürfen nur freigegebene Hosts prüfen und müssen über die bestehende
sichere Netzwerkfunktion mit Limits erfolgen; keine frei nutzbare URL-Prüfproxyfunktion.

Kundenregistrierung wird pro Webseite über „Kunden können sich registrieren“
gesteuert, standardmäßig aus. Die Aktivierung prüft Mailversand und notwendige
Seiten. Der Schalter steuert neue Registrierungen/Beitritte, nicht den Login
bereits zugeordneter Kunden. Globale Kundensperren bleiben davon unabhängig.

## 8. Bewusster Umfang der Folge

In dieser Folge fertig umsetzen: Drei-Schritt-Anlage, zwei Inhaltsvorlagen,
Standardtemplate, Blockeditor, sichere Medienauswahl, Seiten/Bereiche/Menüs,
Autosave mit Konflikterkennung, Revisionen, Vorschau und Veröffentlichung,
Domainstatus sowie die zuvor definierten getrennten Kundenabläufe.

Spätere Erweiterungen sind freie Layoutgestaltung, Mehrsprachigkeitsübersetzungen,
Shop, Zahlungen, Blog, domainübergreifendes SSO und zeitgesteuerte Kampagnen.
Die Architektur bietet dafür Schnittstellen; die Oberfläche zeigt keine leeren
oder funktionslosen Buttons für noch nicht implementierte Erweiterungen.
