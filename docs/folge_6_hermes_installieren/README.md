# Hermes auf Ubuntu installieren

Diese Anleitung gehört zur **Noob2Claw**-Videoserie und beschreibt die Linux-Befehle, um eine bestehende Ubuntu-VM für Hermes vorzubereiten und den Hermes AI-Agent zu installieren.

---

# 🎥 Passendes Video

Wer die komplette Installation Schritt für Schritt sehen möchte, findet
hier das passende Video der Noob2Claw-Serie:

👉 [https://youtu.be/c07kQmEOcUk](https://www.youtube.com/watch?v=oFsuvDHyCq4)

---

# Voraussetzungen

- Ubuntu VM (empfohlen als Klon der OpenClaw-VM)
- Internetverbindung
- SSH-Zugriff
- Benutzer mit sudo-Rechten

---

# 1. Ubuntu aktualisieren

Paketquellen aktualisieren und alle installierten Pakete auf den neuesten Stand bringen.

```bash
sudo apt update
sudo apt upgrade -y
```

---

# 2. Hostname ändern

Der Klon erhält einen eigenen Hostnamen.

```bash
sudo hostnamectl set-hostname hermes
```

Kontrolle:

```bash
hostname
```

---

# 3. System neu starten

```bash
sudo reboot
```

Nach dem Neustart erneut per SSH verbinden.

---

# 4. Hermes installieren

Installationsscript herunterladen und ausführen.

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

---

# 5. Shell neu laden

Damit der neue PATH übernommen wird.

```bash
source ~/.bashrc
```

---

# 6. Installation prüfen

```bash
hermes --version
```

Wird eine Versionsnummer angezeigt, war die Installation erfolgreich.

---

# 7. Hermes starten

Standardmodus:

```bash
hermes
```

Alternativ direkt mit Terminal-Oberfläche (TUI):

```bash
hermes --tui
```

---

# 8. Erstes Setup

Beim ersten Start wird Hermes eingerichtet.

Dabei können unter anderem folgende Einstellungen vorgenommen werden:

- AI-Provider auswählen
- API-Key hinterlegen
- Modell auswählen
- Plugins aktivieren
- Tools konfigurieren

---

# Nützliche Befehle

## Version anzeigen

```bash
hermes --version
```

## Hermes starten

```bash
hermes
```

## Hermes TUI starten

```bash
hermes --tui
```

---

# Typischer Ablauf

1. Ubuntu aktualisieren
2. Hostname ändern
3. Neustarten
4. Hermes installieren
5. Shell neu laden
6. Installation prüfen
7. Hermes starten
8. Erstes Setup durchführen

---

# Ziel

Nach dieser Anleitung besitzt ihr:

- eine OpenClaw-VM
- eine separate Hermes-VM

Beide Systeme können unabhängig voneinander betrieben, getestet und weiterentwickelt werden.
