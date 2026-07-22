# Debian Webserver für Noob2Claw installieren

Diese Anleitung gehört zur **Noob2Claw-Videoserie** und zeigt die
Installation eines schlanken Debian-Servers unter Proxmox inklusive
Apache, PHP 8.4 und MariaDB.

Die VM bildet die Grundlage für das spätere OpenClaw-Webinterface.

# 🌐 Noob2Claw-Webseite

Weitere Informationen, Anleitungen und Neuigkeiten zum Projekt findet ihr auf [noob2claw.de](https://noob2claw.de/).

------------------------------------------------------------------------

# 🎥 Passendes Video

https://www.youtube.com/watch?v=2emtMmx3fZk

------------------------------------------------------------------------

# Voraussetzungen

-   Proxmox
-   Debian ISO
-   Internetverbindung

------------------------------------------------------------------------

# 1. System aktualisieren

``` bash
apt update
apt upgrade -y
reboot
```

# 2. SSH installieren

``` bash
apt install openssh-server -y
systemctl enable ssh
systemctl start ssh
```

# 3. Apache installieren

``` bash
apt install apache2 -y
systemctl enable apache2
systemctl start apache2
```

# 4. PHP 8.4 installieren

``` bash
apt-get install php8.4 php8.4-cgi php8.4-cli php8.4-common php8.4-curl php8.4-gd php8.4-mysql php8.4-xml php8.4-zip php8.4-imagick libapache2-mod-php8.4 -y
```

# 5. MariaDB installieren

``` bash
apt install mariadb-server -y
systemctl enable mariadb
systemctl start mariadb
mariadb-secure-installation
```

# 6. Datenbank anlegen

``` sql
DROP USER IF EXISTS 'noobclaw'@'localhost';

CREATE DATABASE IF NOT EXISTS noobclaw
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

CREATE USER 'noobclaw'@'localhost'
IDENTIFIED BY 'Passwort';

GRANT ALL PRIVILEGES ON noobclaw.* TO 'noobclaw'@'localhost';

FLUSH PRIVILEGES;
```

# 7. Projektverzeichnis

``` bash
mkdir -p /var/www/noobclaw
chown -R www-data:www-data /var/www/noobclaw
chmod -R 755 /var/www/noobclaw
```

# 8. Apache VirtualHost

Datei erstellen:

``` bash
nano /etc/apache2/sites-available/noobclaw.conf
```

Inhalt:

``` apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName noobclaw.local

    DocumentRoot /var/www/noobclaw

    <Directory /var/www/noobclaw>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/noobclaw-error.log
    CustomLog ${APACHE_LOG_DIR}/noobclaw-access.log combined
</VirtualHost>
```

# 9. Apache konfigurieren

``` bash
export PATH=$PATH:/usr/sbin:/sbin

mv /etc/apache2/sites-available/noobclaw.conf /etc/apache2/sites-enabled/noobclaw.conf

rm /etc/apache2/sites-available/000-default.conf

a2enmod rewrite
a2enmod php8.4

systemctl restart apache2
```

Falls erforderlich:

``` bash
apt-get install libapache2-mod-php8.4 -y
a2enmod php8.4
systemctl restart apache2
```

# 10. Testdatei

``` bash
nano /var/www/noobclaw/index.php
```

``` php
<?php

$pdo = new PDO(
    "mysql:host=localhost;dbname=noobclaw;charset=utf8mb4",
    "noobclaw",
    "Passwort"
);

echo "<h1>Noob2Claw</h1>";
echo "PHP funktioniert.<br>";
echo "MariaDB Verbindung erfolgreich.";

?>
```

# 11. Browsertest

    http://IP-Adresse

Erwartete Ausgabe:

-   Noob2Claw
-   PHP funktioniert.
-   MariaDB Verbindung erfolgreich.

------------------------------------------------------------------------

# Wichtige Befehle

``` bash
apt update
apt upgrade -y
apt install openssh-server -y
apt install apache2 -y
apt install mariadb-server -y
systemctl restart apache2
systemctl restart mariadb
```

------------------------------------------------------------------------

# Ziel

Nach dieser Anleitung steht ein vollständiger Debian-LAMP-Server bereit,
der als Grundlage für das OpenClaw-Webinterface der Noob2Claw-Serie
dient.
