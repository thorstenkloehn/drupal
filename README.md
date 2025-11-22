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



