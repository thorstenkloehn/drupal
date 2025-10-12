# Entwicklung mit Git und IDE

## Projekt klonen

Um das Projekt lokal zu entwickeln, klone das Repository mit folgendem Befehl:

```bash
git clone https://github.com/thorstenkloehn/drupal.git
```



## IDE verwenden

Öffne das geklonte Projekt in deiner bevorzugten Entwicklungsumgebung (IDE), z.B. VS Code, PhpStorm oder Eclipse.
## Nginx Konfiguration

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


## Projekt auf Produktserver übertragen

Um die Entwicklungsumgebung auf den Produktserver zu übertragen, kannst du `rsync` verwenden. Dabei soll die Datei `composer.json` ausgeschlossen werden:

```bash
rsync -av 
    --exclude='composer.json' \
    --exclude='composer.lock' \
    --exclude='web/sites/default/files/config_*' \
    --exclude='web/sites/default/settings.php' \
    --exclude='web/sites/default/files' \
    /var/www/drupal/ user@zielserver:/var/www/drupal
```
