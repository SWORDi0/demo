HQ-SRV

apt-get install lamp-server -y

mount /dev/sr0 /mnt/

cp /mnt/web/index.php /var/www/html/
cp /mnt/web/logo.png /var/www/html/

vim /var/www/html/index.php

$servername = "localhost";
$username = "webc";
$password = "P@ssw0rd";
$dbname = "webdb";

systemctl enable --now mariadb

mariadb -u root

CREATE DATABASE webdb;

CREATE USER 'webc'@'localhost' IDENTIFIED BY 'P@ssw0rd';

GRANT ALL PRIVILEGES ON webdb.* TO 'webc'@'localhost';

EXIT;

systemctl enable --now httpd2

mariadb -u webc -p webdb < /mnt/web/dump.sql

enter HQ-CLI and then 192.168.100.2
