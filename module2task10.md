ISP
apt-get install apache2-htpasswd
htpasswd -c /etc/nginx/.htpasswd kazimir

P@ssw0rd
P@ssw0rd

vim /etc/nginx/sites-available.d/default.conf
server {
    listen 80;
    server_name web.au-team.irpo;
    location / {
        proxy_pass http://172.16.1.2:8080;
        auth_basic "Restructed area"; 
        auth_basic_user_file /etc/nginx/.htpasswd; 
    }
}

server {
    listen 80;
    server_name docker.au-team.irpo;
    location / {
        proxy_pass http://172.16.2.2:8080;
    }
}

systemctl restart nginx

on HQ-CLI test

