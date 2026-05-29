BR-SRV
apt-get update && apt-get install docker-engine docker-compose -y

systemctl enable --now docker

mount /dev/sr0 /mnt/

docker load -i /mnt/docker/site_latest.tar
docker load -i /mnt/docker/mariadb_latest.tar

docker images

vim /root/compose.yaml

services:
  database:
    container_name: db
    image: mariadb:10.11
    restart: always
    ports:
      - "3306:3306"
    environment:
      MARIADB_DATABASE: testdb
      MARIADB_USER: test
      MARIADB_PASSWORD: P@ssw0rd
      MARIADB_ROOT_PASSWORD: toor
  app:
    container_name: tespapp
    image: site:latest
    restart: always
    ports:
      - "8080:8000"
    environment:
      DB_TYPE: maria
      DB_HOST: 192.168.3.2
      DB_PORT: "3306"
      DB_NAME: testdb
      DB_USER: test
      DB_PASS: P@ssw0rd
    depends_on:
      - database

docker compose up -d
docker compose down

docker compose ps

docker logs -f tespapp

you can test it in HQ-CLI by searching 192.168.3.2:8080

