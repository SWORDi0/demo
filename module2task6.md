BR-SRV
apt-get update && apt-get install docker-engine docker-compose -y

systemctl enable --now docker

mount /dev/sr0 /mnt/

docker load -i /mnt/docker/site_latest.tar
docker load -i /mnt/docker/postgresql_latest.tar

docker images

vim /root/compose.yaml

services:
  db:
    container_name: db
    image: postgres:15-alpine
    restart: always
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: testdb3
      POSTGRES_USER: test3
      POSTGRES_PASSWORD: P@ssw0rd

  site:
    container_name: site
    image: site:latest
    restart: always
    ports:
      - "8083:8000"
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: "5432"
      DB_NAME: testdb3
      DB_USER: test3
      DB_PASS: P@ssw0rd
    depends_on:
      - db

docker compose up -d
docker compose down

docker compose ps

docker logs -f site

you can test it in HQ-CLI by searching 192.168.3.2:8080

