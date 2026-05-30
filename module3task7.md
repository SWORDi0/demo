#_______________________________________HQ-SRV__________________________________________


vim /var/lib/bind/etc/zone/au-team.irpo

mon  IN   A 192.168.1.10

systemctl restart bind
apt-get install docker-compose docker-engine -y
systemctl enable --now docker
docker pull mariadb && docker pull postgres
vim zabbix.yml

services:
  zabbix-postgres:
    container_name: zabbix_postgres
    image: postgres:16
    volumes:
      - postgres-zabbix:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: zabbix
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbix
    restart: unless-stopped
  zabbix-server:
    container_name: zabbix-server
    image: zabbix/zabbix-server-pgsql
    environment:
      DB_SERVER_HOST: zabbix-postgres
      DB_SERVER_PORT: 5432
      POSTGRES_DB: zabbix
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbix
    ports:
      - "10051:10051"
    restart: unless-stopped
    depends_on:
      - zabbix-postgres
  zabbix-web:
    container_name: zabbix-web
    image: zabbix/zabbix-web-nginx-pgsql
    environment:
      DB_SERVER_HOST: zabbix-postgres
      DB_SERVER_PORT: 5432
      POSTGRES_DB: zabbix
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbix
      ZBX_SERVER_HOST: zabbix-server
      ZBX_SERVER_PORT: 10051
      PHP_TZ: Asia/Yekaterinburg
    ports:
      - "8080:8080"
    restart: unless-stopped
    depends_on:
      - zabbix-postgres
volumes:
  postgres-zabbix:

docker compose -f zabbix.yml up -d

#_______________________________________HQ-CLI__________________________________________

В браузере: http://mon.au-team.irpo:8080

Username: Admin
Password: zabbix

Users > Authentication > Убрать галочку с "Avoid easy-to-guess passwords" > Update

User settings > Profile > Change password:

Current password: zabbix
Password: P@ssw0rd
Password (Once again): P@ssw0rd

Update > OK

# Переавторизоваться с новым паролем.


#___________________________________HQ-SRV+BR-SRV_______________________________________

apt-get install zabbix-agent -y
vim /etc/zabbix/zabbix_agentd.conf

  ServerActive=192.168.1.10

  Server=0.0.0.0/0

systemctl enable --now zabbix_agentd.service

systemctl restart zabbix_agentd.service

#_______________________________________HQ-CLI__________________________________________

Monitoring > Hosts > Найти Zabbix Server > Host > Agent: 192.168.1.10 > Update

# В правом верхнем углу страницы будет функция создание хоста.

# Для хостов требуется указать следующие данные:

#   host name (br-srv.au-team.irpo и hq-srv.au-team.irpo). На функционал это никак не влияет, просто имя в рамках системы.

#   IP адреса устройств  (192.168.1.10 и 192.168.3.10).

#   Добавить шаблоны мониторинга с помощью которых агенты будут отправлять нужные для сервера данные, пошаговая инструкция:

Create host:

Hostname: br-srv.au-team.irpo(hq-srv.au-team.irpo)
Templates: Templates - Linux by Zabbix agent
Host groups: Linux servers
Interfaces: Add > Agent > 192.168.3.10(192.168.1.10)

# На HQ-SRV необходимо перезагрузить zabbix, а на HQ-CLI обновить сайт чтобы применить настройки. Возможно придется подождать.

# Все изменения и получаемые данные находите в Graphs + Latest Data.
