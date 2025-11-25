# Entwicklung mit Git und IDE
## Datenbank
Drupal benötigt eine Datenbank, um Inhalte, Einstellungen und Benutzerinformationen zu speichern. Die Datenbank ermöglicht es, Daten effizient zu verwalten, dynamisch abzurufen und zu aktualisieren. Ohne eine Datenbank könnten Inhalte nicht strukturiert gespeichert oder flexibel genutzt werden.
### PostgreSQL PHP-Treiber installieren

Um Drupal mit einer PostgreSQL-Datenbank zu verwenden, muss der passende PHP-Treiber installiert sein. Installieren Sie den Treiber mit folgendem Befehl:

```bash
sudo -u postgres -i
createdb -E UTF8 -O thorsten drupal
exit # Ausloggen
sudo apt-get install php-pgsql
```
### SQLite3 PHP-Treiber auf dem Ubuntu-Rechner installieren

Um Drupal mit einer SQLite3-Datenbank zu verwenden, muss der passende PHP-Treiber installiert sein. Installieren Sie den Treiber mit folgendem Befehl:

```bash
sudo apt-get update
sudo apt-get install php-sqlite3
```

Starten Sie anschließend den Webserver neu, damit die Änderungen wirksam werden:

```bash

sudo systemctl restart php8.4-fpm
sudo systemctl restart nginx
```
### Nginx Konfigurationsdatei erstellen
Erstelle eine neue Konfigurationsdatei mit folgendem Befehl:
```
sudo nano /etc/nginx/conf.d/drupal.conf
```
Füge zum Beispiel folgende Grundkonfiguration ein:
```
server {
    listen 80;
    server_name localhost;
    root /var/www/drupal/web;

    index index.php index.html index.htm;



    # Sicherheitsheader
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
    add_header X-XSS-Protection "1; mode=block";

    # Gzip-Komprimierung
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    location = /favicon.ico { log_not_found off; access_log off; }
    location = /robots.txt  { allow all; log_not_found off; access_log off; }

    location ~ \..*/.*\.php$ { return 403; }
    location ~ ^/sites/.*/private/ { return 403; }
    location ~ ^/sites/[^/]+/files/.*\.php$ { deny all; }
    location ~ (^|/)\. { return 403; }

    location / {
        try_files $uri /index.php?$query_string;
    }

    location @rewrite {
        rewrite ^/(.*)$ /index.php?q=$1;
    }

    location ~ /vendor/.*\.php$ {
        deny all;
        return 404;
    }

    location ~ '\.php$|^/update.php' {
        include fastcgi_params;
        fastcgi_split_path_info ^(.+?\.php)(/.*)?$;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param HTTP_PROXY "";
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
        fastcgi_intercept_errors on;
    }

    location ~ ^/sites/.*/files/styles/ {
        try_files $uri @rewrite;
    }

    location ~ ^(/[a-z\-]+)?/system/files/ {
        try_files $uri /index.php?$query_string;
    }

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        try_files $uri @rewrite;
        expires max;
        log_not_found off;
    }
}
```




