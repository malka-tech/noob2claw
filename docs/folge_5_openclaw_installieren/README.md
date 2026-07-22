# OpenClaw auf Ubuntu installieren

Diese Anleitung gehört zur **Noob2Claw-Videoserie** und beschreibt die
wichtigsten Linux-Befehle, um OpenClaw auf einer Ubuntu-VM unter Proxmox
zu installieren.

Am Ende läuft OpenClaw direkt auf einer eigenen Ubuntu-VM und kann über
die Kommandozeile, die TUI und das Web-Dashboard verwendet werden.

# 🌐 Noob2Claw-Webseite

Weitere Informationen, Anleitungen und Neuigkeiten zum Projekt findet ihr auf [noob2claw.de](https://noob2claw.de/).

------------------------------------------------------------------------

# 🎥 Passendes Video

Wer die komplette Installation Schritt für Schritt sehen möchte, findet
hier das passende Video der Noob2Claw-Serie:

👉 https://youtu.be/c07kQmEOcUk

------------------------------------------------------------------------

# Voraussetzungen

-   Proxmox-Server
-   Ubuntu-VM oder vorbereitete Ubuntu-Vorlage
-   Internetverbindung
-   SSH-Zugriff auf die VM
-   Benutzer mit `sudo`-Rechten

Empfohlen wird eine separate Ubuntu-VM ausschließlich für OpenClaw.

------------------------------------------------------------------------

# 1. Ubuntu aktualisieren

``` bash
sudo apt update
sudo apt upgrade -y
```

# 2. curl installieren

``` bash
sudo apt install curl -y
```

# 3. OpenClaw installieren

``` bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

# 4. PATH dauerhaft hinzufügen

``` bash
echo 'export PATH="$HOME/.openclaw/bin:$PATH"' >> ~/.bashrc
```

# 5. Shell neu laden

``` bash
source ~/.bashrc
```

# 6. Installation prüfen

``` bash
openclaw --version
```

# 7. OpenClaw starten

``` bash
openclaw
```

oder

``` bash
openclaw tui
```

------------------------------------------------------------------------

# Backup

``` bash
tar -czf "openclaw-backup-$(date +%Y-%m-%d).tar.gz" ~/.openclaw
```

------------------------------------------------------------------------

# Komplette Installation

``` bash
sudo apt update
sudo apt upgrade -y
sudo apt install curl -y

curl -fsSL https://openclaw.ai/install.sh | bash

echo 'export PATH="$HOME/.openclaw/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

openclaw --version
openclaw
```
