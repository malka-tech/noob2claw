\# Noob2Claw – Folge 12: Wir bauen unser Wiki per Vibe Coding



\# Beispielprompts für die Entwicklung eines neuen Wikis



Version: 1.0  

Bereich: Folge 12  

Ausgangslage: Im Noob2Claw-System existiert noch kein Wiki  

Ziel: Ein neues, professionelles Wiki schrittweise per Vibe Coding entwickeln



\---



\# Grundidee dieser Folge



In dieser Folge wird das Wiki nicht anhand einer vollständig ausformulierten technischen Spezifikation gebaut.



Statt jede Tabelle, jede Datei, jedes Datenbankfeld und jede Funktion vorzugeben, beschreiben wir:



\- welches Problem gelöst werden soll

\- wie sich das fertige Wiki anfühlen soll

\- welche Kernfunktionen benötigt werden

\- welche bestehenden Noob2Claw-Regeln eingehalten werden müssen

\- welche Schwächen wir nach dem ersten Entwicklungsstand erkennen



Danach wird mit kurzen, eigenen Folgeprompts weitergearbeitet.



```text

Vibe Coding bedeutet:

Ziel vorgeben → Ergebnis prüfen → selbst nachdenken → gezielt nachprompten

```



Es bedeutet ausdrücklich nicht:



```text

Einen ungenauen Prompt absenden und ungeprüft alles übernehmen.

```



\---



\# So können die Beispielprompts genutzt werden



Die nachfolgenden Prompts sind keine starre Aufgabenliste, die vollständig und exakt in dieser Reihenfolge abgearbeitet werden muss.



Sie dienen als Beispiele dafür, wie man:



\- mit einem größeren Zielprompt startet

\- das erste Ergebnis selbst prüft

\- fehlende Funktionen erkennt

\- mit kurzen Folgeprompts verbessert

\- der KI Raum für eigene technische Entscheidungen lässt



Wichtig ist, dass man nach jedem Entwicklungsschritt das Ergebnis im Browser, in der Datenbank und im Code kontrolliert.



\---



\# Beispielprompt 1 – Neues Wiki als ersten funktionsfähigen Stand entwickeln



```text

Du arbeitest am bestehenden Projekt Noob2Claw.



Im System existiert aktuell noch kein Wiki.



Lade zuerst den aktuellen Stand der Projektdokumentation und analysiere anschließend das aktive Projekt unter /mnt/noob2claw/ vollständig.



Prüfe insbesondere die bestehende Architektur, Navigation, Benutzerverwaltung, Rechteverwaltung, Datenbankfunktionen, Formulare, Tabellen, CSS-Standards, JavaScript-Struktur, Fehlerbehandlung und Logs.



Entwickle danach einen ersten vollständig nutzbaren Wiki-Bereich innerhalb von Noob2Claw.



Das Wiki soll als zentrale Wissensdatenbank für interne Dokumentationen, technische Anleitungen, Projektwissen, Prozesse und später auch als Wissensquelle für KI-Agenten dienen.



Der erste Stand soll mindestens enthalten:



\- Wiki-Startseite

\- Kategorien

\- Artikelübersicht

\- Artikel erstellen

\- Artikel bearbeiten

\- Artikel anzeigen

\- Markdown-Inhalte

\- Suchfunktion

\- Status für Entwurf und veröffentlicht

\- Integration in Navigation und Rechteverwaltung

\- professionelles und übersichtliches Design



Entscheide selbstständig, welche Datenbanktabellen, Felder, Funktionen, Navigationsseiten, Rechte und Styles dafür sinnvoll sind.



Halte dich vollständig an die bestehenden Noob2Claw-Standards und baue kein separates System neben der vorhandenen Architektur auf.



Erstelle keine Platzhalter und keine reine Demo. Der erste Stand muss im Browser nutzbar sein.



Prüfe nach der Umsetzung die PHP-Syntax, die Datenbankzugriffe, die Rechteprüfung und die wichtigsten Bedienabläufe.



Gib anschließend einen strukturierten Bericht über alle erstellten und geänderten Dateien, Tabellen, Rechte, Funktionen, Tests und noch offenen Punkte aus.

```



\## Warum dieser Prompt bewusst nicht alles vorgibt



Der Prompt definiert:



\- das Ziel

\- die Ausgangslage

\- die Mindestfunktionen

\- die verbindlichen Leitplanken



Er definiert nicht jede Datei und jedes Datenbankfeld. Die KI muss das vorhandene Projekt selbst analysieren und eine passende Lösung entwickeln.



\---



\# Beispielprompt 2 – Wiki-Kategorien und Struktur verbessern



```text

Prüfe den aktuellen Wiki-Bereich aus Sicht eines Benutzers, der später mehrere hundert Artikel verwalten muss.



Die bisherige Kategoriestruktur ist noch nicht ausreichend durchdacht.



Überarbeite das Wiki so, dass Inhalte sauber über Kategorien organisiert werden können.



Berücksichtige dabei beispielsweise:



\- Hauptkategorien

\- optionale Unterkategorien

\- verständliche Sortierung

\- Kategorieübersicht auf der Wiki-Startseite

\- Anzahl der enthaltenen Artikel

\- Beschreibung einer Kategorie

\- aktive und archivierte Kategorien

\- eindeutige Zuordnung eines Artikels

\- sinnvolles Verhalten beim Löschen oder Archivieren einer Kategorie



Entscheide selbstständig, welche Funktionen und Datenbankanpassungen sinnvoll sind.



Die Bedienung soll auch bei vielen Kategorien übersichtlich bleiben.



Prüfe außerdem, ob Kategorien nur optisch oder auch serverseitig durch Rechte und Sichtbarkeit geschützt werden müssen.



Setze die Verbesserungen vollständig um und teste anschließend das Anlegen, Bearbeiten, Sortieren, Archivieren und Anzeigen von Kategorien.

```



\## Eigene mögliche Folgeprompts



```text

Die Kategorienavigation wirkt noch zu technisch. Gestalte sie wie ein modernes Wissensportal und reduziere unnötige Tabellenansichten.

```



```text

Ergänze eine sinnvolle Breadcrumb-Navigation, damit man bei Unterkategorien jederzeit erkennt, wo man sich befindet.

```



```text

Prüfe, ob Artikel mehreren Kategorien zugeordnet werden sollten. Entscheide anhand der bisherigen Architektur und erkläre deine Entscheidung, bevor du sie umsetzt.

```



\---



\# Beispielprompt 3 – Datei-Upload und Anhänge ergänzen



```text

Das Wiki soll nicht nur Text enthalten.



Erweitere den bestehenden Wiki-Bereich um eine professionelle Datei- und Anhangsverwaltung.



Benutzer sollen Dateien zu Wiki-Artikeln hochladen und später wieder anzeigen oder herunterladen können.



Typische Dateien sind:



\- Bilder

\- Screenshots

\- PDF-Dokumente

\- Markdown-Dateien

\- Textdateien

\- Office-Dokumente

\- technische Dokumentationen



Analysiere selbstständig, wie der Upload sicher in die vorhandene Noob2Claw-Architektur integriert werden kann.



Achte zwingend auf:



\- erlaubte Dateitypen

\- maximale Dateigröße

\- serverseitige MIME-Type-Prüfung

\- sichere Dateinamen

\- keine direkte Ausführung hochgeladener Dateien

\- Schutz vor Pfadmanipulation

\- Speicherung außerhalb frei ausführbarer Bereiche oder gesicherte Auslieferung

\- Zuordnung zum richtigen Wiki-Artikel

\- Berechtigungsprüfung beim Upload und Download

\- Löschen oder Archivieren von Anhängen

\- Anzeige von Dateiname, Größe, Typ, Uploader und Upload-Zeitpunkt

\- sinnvolle Vorschau für Bilder

\- verständliche Fehlermeldungen



Die hochgeladenen Dateien dürfen nicht einfach ungeschützt über eine erratbare URL erreichbar sein.



Entscheide selbst, welche Tabellen, Funktionsdateien, API-Aktionen oder Download-Routen benötigt werden.



Erstelle eine übersichtliche Anhangssektion innerhalb des Artikels und eine komfortable Upload-Möglichkeit im Editor.



Teste anschließend mindestens Bild-Upload, PDF-Upload, ungültigen Dateityp, zu große Datei, fehlende Berechtigung, Download und Löschen.

```



\## Eigene mögliche Folgeprompts



```text

Der Upload funktioniert, aber die Darstellung ist noch unübersichtlich. Gestalte die Anhänge wie eine professionelle Dateiliste mit Vorschau, Dateityp, Größe und Aktionen.

```



```text

Ergänze Drag-and-drop für den Datei-Upload, ohne ein zusätzliches Frontend-Framework einzuführen.

```



```text

Prüfe die gesamte Upload-Implementierung gezielt auf Sicherheitslücken und behebe alle gefundenen Probleme direkt.

```



\---



\# Beispielprompt 4 – Artikel-Editor sinnvoll ausbauen



```text

Prüfe den aktuellen Wiki-Editor und verbessere ihn so, dass sich auch längere technische Dokumentationen komfortabel erstellen lassen.



Benötigt werden mindestens:



\- Artikeltitel

\- Kategorie

\- Kurzbeschreibung

\- Markdown-Inhalt

\- Entwurf oder veröffentlicht

\- Speichern

\- Vorschau

\- Hinweis bei ungespeicherten Änderungen

\- verständliche Validierung

\- übersichtliche Darstellung der Anhänge



Entscheide selbstständig, welche zusätzlichen Funktionen wirklich nützlich sind, ohne den Editor zu überladen.



Mögliche sinnvolle Erweiterungen sind:



\- automatische URL-Bezeichnung oder Slug

\- Inhaltsverzeichnis aus Überschriften

\- Markdown-Hilfe

\- Vollbildmodus

\- Zeichen- oder Wortanzahl

\- Autosave für Entwürfe

\- zuletzt gespeichert



Achte besonders darauf, dass gerendertes Markdown sicher ausgegeben wird und kein ungeprüftes HTML oder JavaScript ausgeführt werden kann.



Überarbeite sowohl Bedienung als auch technische Sicherheit und teste den vollständigen Ablauf vom neuen Artikel bis zur veröffentlichten Ansicht.

```



\---



\# Beispielprompt 5 – Wiki-Suche brauchbar machen



```text

Die aktuelle Wiki-Suche ist noch zu einfach.



Überarbeite sie so, dass Benutzer auch bei vielen Artikeln schnell relevante Inhalte finden.



Die Suche soll nicht nur exakte Titel finden, sondern nach Möglichkeit berücksichtigen:



\- Artikeltitel

\- Kurzbeschreibung

\- Inhalt

\- Kategorie

\- Schlagwörter

\- Dateinamen von Anhängen



Prüfe selbstständig, welche Suchlogik mit der bestehenden MariaDB-Struktur sinnvoll und performant umsetzbar ist.



Die Suchergebnisse sollen übersichtlich darstellen:



\- Titel

\- Kategorie

\- passenden Textausschnitt

\- Änderungsdatum

\- Status

\- Treffergrund oder hervorgehobenen Suchbegriff, sofern sinnvoll



Berücksichtige Rechte und Sichtbarkeit. Ein Benutzer darf über die Suche keine Inhalte oder Dateinamen sehen, auf die er keinen Zugriff hat.



Setze die Suche vollständig um und teste leere Suchbegriffe, Teilbegriffe, mehrere Treffer, keine Treffer, Sonderzeichen und fehlende Berechtigungen.

```



\---



\# Beispielprompt 6 – Versionierung und Änderungshistorie ergänzen



```text

Ein Wiki muss Änderungen nachvollziehbar machen.



Prüfe die aktuelle Umsetzung und ergänze eine sinnvolle Versionierung für Wiki-Artikel.



Bei jedem relevanten Speichern soll nachvollziehbar sein:



\- wer geändert hat

\- wann geändert wurde

\- welche Version entstanden ist

\- welcher Inhalt vorher vorhanden war

\- optional eine kurze Änderungsnotiz



Benutzer mit passendem Recht sollen:



\- frühere Versionen ansehen

\- Versionen vergleichen

\- eine ältere Version wiederherstellen



Die Wiederherstellung darf die Historie nicht zerstören, sondern soll eine neue Version erzeugen.



Entscheide selbstständig, wie die Versionen effizient gespeichert werden und welche Rechte benötigt werden.



Achte darauf, dass die Oberfläche auch bei vielen Versionen verständlich bleibt.



Teste Bearbeitung, mehrere Versionen, Vergleich, Wiederherstellung und fehlende Berechtigung.

```



\---



\# Beispielprompt 7 – Rechte und Sichtbarkeit sauber lösen



```text

Prüfe den gesamten Wiki-Bereich auf eine saubere Integration in die bestehende Noob2Claw-Rechteverwaltung.



Unterscheide mindestens zwischen:



\- Wiki anzeigen

\- Artikel anlegen

\- eigene Artikel bearbeiten

\- alle Artikel bearbeiten

\- Artikel veröffentlichen

\- Artikel archivieren

\- Kategorien verwalten

\- Dateien hochladen

\- Dateien löschen

\- Versionen ansehen

\- Versionen wiederherstellen



Prüfe zusätzlich, ob einzelne Kategorien nur für bestimmte Benutzer oder Gruppen sichtbar sein sollten.



Wichtig:



\- Rechte müssen serverseitig geprüft werden.

\- Das reine Ausblenden eines Buttons reicht nicht.

\- Suche, Artikelansicht, Dateidownload und Direktaufrufe müssen dieselben Rechte berücksichtigen.

\- Benutzer dürfen durch manipulierte IDs keine fremden oder gesperrten Inhalte öffnen.



Analysiere die bestehende Rechtearchitektur und integriere das Wiki ohne paralleles Berechtigungssystem.



Führe anschließend gezielte Negativtests mit fehlenden Rechten und manipulierten Aufrufen durch.

```



\---



\# Beispielprompt 8 – Wiki-Oberfläche professionell gestalten



```text

Die Wiki-Funktionen sind grundsätzlich vorhanden.



Überarbeite jetzt die Benutzeroberfläche so, dass das Wiki wie ein professionelles internes Wissensportal aussieht und nicht wie eine einfache Verwaltungsseite.



Achte besonders auf:



\- klare Wiki-Startseite

\- dominantes Suchfeld

\- übersichtliche Kategorien

\- zuletzt geänderte Artikel

\- angepinnte oder wichtige Artikel

\- gute Lesbarkeit langer Texte

\- sauberes Inhaltsverzeichnis

\- Breadcrumbs

\- verständliche Statusanzeigen

\- hochwertige Dateidarstellung

\- sinnvolle Leerzustände

\- responsive Darstellung

\- bestehende Noob2Claw-Designstandards



Vermeide eine überladene Oberfläche und unnötige Widgets.



Analysiere den aktuellen Stand selbstständig und setze die wichtigsten Verbesserungen direkt um.



Nutze keine zusätzlichen UI-Frameworks und baue kein zweites Designsystem auf.

```



\---



\# Beispielprompt 9 – Das Wiki selbstständig auf Schwächen prüfen lassen



```text

Prüfe den vollständigen Wiki-Bereich jetzt wie ein erfahrener Softwarearchitekt, Sicherheitstester und Produktdesigner.



Suche selbstständig nach:



\- fehlenden Wiki-Funktionen

\- unklarer Bedienung

\- doppelten Implementierungen

\- falscher Nutzung vorhandener Noob2Claw-Strukturen

\- fehlenden Rechteprüfungen

\- unsicheren Uploads

\- unsicheren Dateidownloads

\- XSS durch Markdown oder Dateinamen

\- SQL-Injection

\- manipulierten IDs

\- fehlerhafter Versionierung

\- schlechter mobiler Darstellung

\- Problemen bei langen Artikeln

\- Problemen bei sehr vielen Kategorien, Artikeln oder Dateien

\- fehlenden Logs und Fehlermeldungen



Erstelle keine reine Bewertung.



Behebe alle gefundenen Probleme direkt, soweit dies ohne neue fachliche Vorgaben möglich ist.



Führe danach die relevanten Tests erneut durch und dokumentiere ehrlich, was erfolgreich war und welche Punkte noch offen sind.

```



\---



\# Beispielprompt 10 – Wiki für spätere KI-Agenten vorbereiten



```text

Das Wiki soll später nicht nur von Menschen gelesen werden, sondern auch als strukturierte Wissensquelle für Noob2Claw-Agenten dienen.



Prüfe, welche Vorbereitungen bereits jetzt sinnvoll sind, ohne in dieser Folge ein vollständiges RAG- oder Vektordatenbanksystem zu bauen.



Berücksichtige beispielsweise:



\- eindeutige Artikel-IDs

\- stabile Kategorien

\- saubere Metadaten

\- Schlagwörter

\- Änderungszeitpunkt

\- Veröffentlichungsstatus

\- klare Textstruktur

\- maschinenlesbare Ausgabe über die bestehende API

\- sichere Rechteprüfung auch bei Agentenzugriffen

\- saubere Trennung zwischen Entwurf und veröffentlichtem Wissen



Entwickle nur die Grundlagen, die zum aktuellen Wiki passen und später sinnvoll erweitert werden können.



Baue keine unnötig komplexe KI-Infrastruktur und erfinde keine Funktionen, die aktuell nicht gebraucht werden.



Dokumentiere am Ende, wie Agenten später kontrolliert auf freigegebene Wiki-Inhalte zugreifen könnten.

```



\---



\# Beispielprompt 11 – Abschließender Funktionstest



```text

Führe einen vollständigen End-to-End-Test des neuen Wiki-Bereichs durch.



Teste mindestens:



1\. Kategorie anlegen

2\. Unterkategorie anlegen, falls unterstützt

3\. Artikel als Entwurf anlegen

4\. Markdown-Inhalt speichern

5\. Artikelvorschau öffnen

6\. Bild hochladen

7\. PDF hochladen

8\. unzulässige Datei ablehnen

9\. Artikel veröffentlichen

10\. Artikel über die Suche finden

11\. Artikel bearbeiten

12\. neue Version erzeugen

13\. ältere Version ansehen

14\. ältere Version wiederherstellen

15\. Datei herunterladen

16\. Datei löschen oder archivieren

17\. Kategorie archivieren

18\. Zugriffe mit fehlenden Rechten blockieren

19\. direkten manipulierten Aufruf blockieren

20\. Darstellung auf schmalem Bildschirm prüfen



Führe außerdem PHP-Syntaxprüfungen für alle geänderten PHP-Dateien durch.



Behebe gefundene Fehler direkt und wiederhole den betroffenen Test.



Gib danach einen strukturierten Abschlussbericht aus mit:



\- umgesetzten Funktionen

\- erstellten Dateien

\- geänderten Dateien

\- Datenbankänderungen

\- neuen Rechten

\- Sicherheitsmaßnahmen

\- erfolgreich durchgeführten Tests

\- fehlgeschlagenen Tests

\- bekannten Einschränkungen

\- sinnvollen nächsten Ausbaustufen



Behaupte keine erfolgreiche Funktion, die nicht tatsächlich geprüft wurde.

```



\---



\# Beispiele für kurze spontane Folgeprompts



Diese kurzen Prompts entstehen direkt aus der eigenen Prüfung des Ergebnisses.



```text

Die Wiki-Startseite ist zu leer. Zeige dort Kategorien, zuletzt bearbeitete Artikel und ein großes Suchfeld, ohne die Seite mit Widgets zu überladen.

```



```text

Beim Öffnen eines Artikels fehlt ein Inhaltsverzeichnis. Erzeuge es automatisch aus den Markdown-Überschriften.

```



```text

Die Dateianhänge sehen wie eine technische Tabelle aus. Gestalte sie benutzerfreundlicher und zeige für Bilder eine Vorschau.

```



```text

Man erkennt nicht, ob ein Artikel Entwurf oder veröffentlicht ist. Verbessere die Statusanzeige in Übersicht, Editor und Artikelansicht.

```



```text

Die Suche findet den Artikelinhalt nicht zuverlässig. Analysiere die Ursache und verbessere die Suchlogik.

```



```text

Beim Löschen einer Kategorie könnten Artikel ohne Zuordnung bleiben. Entwickle eine sichere und verständliche Lösung dafür.

```



```text

Prüfe, ob hochgeladene SVG-, HTML- oder PHP-Dateien ein Sicherheitsrisiko darstellen, und sperre gefährliche Formate konsequent.

```



```text

Die mobile Darstellung des Editors ist unbrauchbar. Überarbeite sie anhand der bestehenden Responsive-Standards.

```



```text

Finde selbst die drei größten Schwächen der aktuellen Wiki-Bedienung und behebe sie.

```



\---



\# Was man beim eigenen Prompten beachten sollte



\## Ein Prompt sollte ein klares Ziel besitzen



Schlecht:



```text

Mach das Wiki besser.

```



Besser:



```text

Die Wiki-Startseite zeigt aktuell nur eine Artikeltabelle. Baue eine übersichtliche Einstiegsseite mit Suche, Kategorien und zuletzt geänderten Artikeln. Nutze das bestehende Designsystem und halte die Seite bewusst ruhig.

```



\## Beobachtungen konkret benennen



Statt eine komplette technische Lösung vorzugeben, sollte man beschreiben:



\- was aktuell nicht funktioniert

\- warum es unverständlich ist

\- welches Ergebnis erwartet wird

\- welche Regeln nicht verletzt werden dürfen



\## Der KI nicht blind vertrauen



Nach jedem größeren Schritt prüfen:



\- Funktioniert es wirklich?

\- Passt es zur vorhandenen Architektur?

\- Sind die Rechte serverseitig umgesetzt?

\- Sind Upload und Download sicher?

\- Bleiben bestehende Funktionen intakt?

\- Wurden echte Tests durchgeführt?



\## Nicht alles in einen Prompt stopfen



Ein guter Ablauf ist:



```text

Ersten Stand bauen

&#x20;       ↓

Im Browser testen

&#x20;       ↓

Schwächen erkennen

&#x20;       ↓

Kurzen Folgeprompt schreiben

&#x20;       ↓

Erneut testen

```



\---



\# Zielzustand nach Folge 12



Nach der Folge soll Noob2Claw erstmals über ein eigenes Wiki verfügen.



Das Wiki soll mindestens können:



\- Kategorien verwalten

\- Artikel erstellen und bearbeiten

\- Markdown darstellen

\- Artikel suchen

\- Entwürfe und veröffentlichte Inhalte unterscheiden

\- Dateien und Bilder sicher hochladen

\- Anhänge kontrolliert ausliefern

\- Rechte berücksichtigen

\- Änderungen nachvollziehbar machen

\- professionell und übersichtlich aussehen



Die wichtigste Lernerfahrung der Folge lautet:



```text

Nicht jeder gute KI-Entwicklungsprozess beginnt mit einem riesigen Masterprompt.



Ein klares Ziel, ein prüfbarer erster Stand und gute eigene Folgeprompts sind oft wirkungsvoller.

```

