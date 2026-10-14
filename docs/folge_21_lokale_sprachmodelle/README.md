# Noob2Claw – Folge 21: Lokale KI-Sprachmodelle nutzen

Ziel: Audio auf einem eigenen Server transkribieren und Sprachausgabe erzeugen,
statt dafür Grok oder OpenAI aufzurufen. Das bestehende Textmodell bleibt davon
unabhängig. Lokales Audio bedeutet nicht automatisch vollständig lokalen Chat.

Status: Konzeptentwurf. Schnittstellenanalyse erfolgt;
die Installation des vorgesehenen Referenzservers ist noch nicht verifiziert.
Dies ist keine bereits implementierte oder abgenommene Noob2Claw-Funktion.

## Arbeitsunterlagen

- [0_Startprompt.md](0_Startprompt.md): Umsetzungsauftrag und Grenzen.
- [1_Serverinstallation.md](1_Serverinstallation.md): Installationsablauf und
  noch offene Nachweise vor einer reproduzierbaren Installation.
- [2_Integration.md](2_Integration.md): Adaptervertrag und Bedienung.
- [3_Abnahme.md](3_Abnahme.md): Umstellung, Tests und Rückweg.

## Verbindliche Abgrenzung

Die geprüfte Referenzintegration `videoserver` verwendet `whisper` und `speech`
als API-Modellkennungen. Eine separate Integration `speechserver` verwendet
`whisper-large-v3` und `cosyvoice-3`. Diese Verträge nicht vermischen.
API-Kennungen beweisen weder die installierte Modellversion noch GPU-Ausstattung.
Vor Abschluss der Installationsanleitung muss feststehen, welcher Gateway auf
dem Zielserver bereitgestellt wird. Standard dieses Entwurfs ist der ausdrücklich
angefragte Videoserver-Vertrag; andere Profile sind kein stiller Ersatz.

Die Webseitenfolge wurde nach [Folge 22](../folge_22_webseite/README.md) verschoben.
Keine internen Adressen, Tokens, privaten Stimmen oder Produktionskonfigurationen
in Git aufnehmen. Nur Zuschauerunterlagen, kein internes Präsentationskonzept.
