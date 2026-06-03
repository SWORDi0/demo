HQ-SRV
dont forget to check what in the images directory
apt-get install lamp-server -y

mount /dev/sr0 /mnt/

cp /mnt/web/index.php /var/www/html/
cp /mnt/web/images /var/www/html/

vim /var/www/html/index.php

$servername = "localhost";
$username = "web3";
$password = "P@ssw0rd";
$dbname = "webdb";

systemctl enable --now mariadb

mariadb -u root

CREATE DATABASE webdb;

CREATE USER 'web3'@'localhost' IDENTIFIED BY 'P@ssw0rd';

GRANT ALL PRIVILEGES ON webdb.* TO 'web3'@'localhost';

EXIT;

systemctl enable --now httpd2

mariadb -u web3 -p webdb < /mnt/web/dump.sql

enter HQ-CLI and then 192.168.100.2
