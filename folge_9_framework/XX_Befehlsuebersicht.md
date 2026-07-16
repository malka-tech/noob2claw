# Befehlsübersicht – Noob2Claw Folge 09

# Debian – SSH vorbereiten

```bash
sudo apt update
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
sudo systemctl status ssh

hostname -I
```

---

# Debian – Webroot vorbereiten

```bash
sudo mkdir -p /var/www/noobclaw

sudo chown -R www-data:www-data /var/www/noobclaw

sudo find /var/www/noobclaw \
    -type d \
    -exec chmod 755 {} \;

sudo find /var/www/noobclaw \
    -type f \
    -exec chmod 755 {} \;
```

---

# Debian – www-data vorbereiten

```bash
getent passwd www-data

sudo usermod -s /bin/bash www-data
```

---

# Debian – SSH-Verzeichnis

```bash
sudo install \
    -d \
    -m 700 \
    -o www-data \
    -g www-data \
    /var/www/.ssh

sudo install \
    -m 600 \
    -o www-data \
    -g www-data \
    /dev/null \
    /var/www/.ssh/authorized_keys
```

---

# Debian – SSH-Key hinterlegen

```bash
sudo nano /var/www/.ssh/authorized_keys
```

Danach:

```bash
sudo chown -R www-data:www-data /var/www/.ssh
sudo chmod 700 /var/www/.ssh
sudo chmod 600 /var/www/.ssh/authorized_keys
```

---

# Debian – Passwortlogin deaktivieren

```bash
sudo nano \
/etc/ssh/sshd_config.d/99-noob2claw-www-data.conf
```

Inhalt:

```text
Match User www-data
    PubkeyAuthentication yes
    PasswordAuthentication no
    KbdInteractiveAuthentication no
    AllowAgentForwarding no
    AllowTcpForwarding no
    X11Forwarding no
    PermitTTY no
```

Prüfen:

```bash
sudo sshd -t
sudo systemctl reload ssh
```

---

# Debian – Hostkey anzeigen

```bash
sudo ssh-keygen \
    -lf \
    /etc/ssh/ssh_host_ed25519_key.pub
```

---

# Ubuntu – SSHFS installieren

```bash
sudo apt update
sudo apt install -y sshfs openssh-client
```

---

# Ubuntu – Mountpunkt

```bash
sudo install \
    -d \
    -m 755 \
    -o noobclaw \
    -g noobclaw \
    /mnt/noob2claw
```

---

# Ubuntu – Benutzer wechseln

```bash
sudo -iu noobclaw
```

---

# Ubuntu – SSH-Key erzeugen

```bash
install -d -m 700 ~/.ssh

ssh-keygen \
    -t ed25519 \
    -a 100 \
    -f ~/.ssh/id_ed25519_noob2claw \
    -C "noob2claw-sshfs-mount" \
    -N ""
```

---

# Ubuntu – Public Key anzeigen

```bash
cat ~/.ssh/id_ed25519_noob2claw.pub
```

---

# Ubuntu – Hostkey übernehmen

```bash
ssh-keyscan \
    -t ed25519 \
    192.168.178.XX \
    > /tmp/noobclaw_hostkey

ssh-keygen -lf /tmp/noobclaw_hostkey

cat /tmp/noobclaw_hostkey >> ~/.ssh/known_hosts

chmod 600 ~/.ssh/known_hosts

rm /tmp/noobclaw_hostkey
```

---

# Ubuntu – SSH testen

```bash
ssh \
    -i ~/.ssh/id_ed25519_noob2claw \
    www-data@192.168.178.XX
```

---

# Ubuntu – SSHFS mounten

```bash
sshfs \
    www-data@192.168.178.XX:/var/www/noobclaw \
    /mnt/noob2claw \
    -o IdentityFile=~/.ssh/id_ed25519_noob2claw \
    -o IdentitiesOnly=yes \
    -o idmap=user \
    -o reconnect \
    -o ServerAliveInterval=15 \
    -o ServerAliveCountMax=3 \
    -o default_permissions
```

---

# Mount prüfen

```bash
findmnt /mnt/noob2claw

ls -la /mnt/noob2claw
```

---

# Schreibtest

```bash
echo "SSHFS funktioniert" > /mnt/noob2claw/test.txt

rm /mnt/noob2claw/test.txt
```

---

# systemd-Dienst

```bash
sudo nano \
/etc/systemd/system/noob2claw-sshfs.service
```

---

# systemd aktivieren

```bash
fusermount3 -u /mnt/noob2claw

sudo systemctl daemon-reload

sudo systemctl enable --now noob2claw-sshfs.service

sudo systemctl status noob2claw-sshfs.service

findmnt /mnt/noob2claw

sudo journalctl \
-u noob2claw-sshfs.service \
-n 100 \
--no-pager
```

---

# Neustarttest

```bash
sudo reboot
```

Nach dem Neustart:

```bash
systemctl is-enabled noob2claw-sshfs.service

systemctl is-active noob2claw-sshfs.service

findmnt /mnt/noob2claw
```

---

# OpenClaw starten

```bash
sudo -iu noobclaw

openclaw tui
```

Prompt:

```text
Führe die Datei

/home/noob2claw/prompts/00_Startprompt.md

vollständig aus und erstelle das Projekt.

Beachte alle weiteren MD-Dateien unter
/home/noob2claw/prompts
als verbindliche Projektdokumentation.

Arbeite ausschließlich im gemounteten
Projektverzeichnis /mnt/noob2claw.

Teste die Anwendung über

http://192.168.178.XX
```

---

# PHP-Syntax prüfen

```bash
find /mnt/noob2claw \
-type f \
-name "*.php" \
-print0 | xargs -0 -n1 php -l
```
