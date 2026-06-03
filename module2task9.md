ISP
apt-get install nginx -y
vim /etc/nginx/sites-available.d/default.conf

server {
    listen 80;
    server_name web.au-team.irpo;
    location / {
        proxy_pass http://172.16.1.2:8083;
    }
}

server {
    listen 80;
    server_name docker.au-team.irpo;
    location / {
        proxy_pass http://172.16.2.2:8083;
    }
}

ln -sf /etc/nginx/sites-available.d/default.conf /etc/nginx/sites-enabled.d/
systemctl enable --now nginx

HQ-CLI
vim /etc/hosts
172.16.1.1 web.au-team.irpo
172.16.2.1 docker.au-team.irpo

if it is works you can enter the full domain name and get the result
