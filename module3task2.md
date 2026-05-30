
  #_______________________________________HQ-SRV__________________________________________
openssl req -newkey rsa:4096 -nodes -keyout ca.key -x509 -days 365 -out ca.crt

	Enter
	HMAO
	RADUZHNY
	BY
	408
	AU-Team CA
	Enter

openssl genrsa -out web.key 4096

openssl req -key web.key -new -out web.csr

	ENTER
	HMAO
	RADUZHNY
	BY
	408
	au-team.irpo
	ENTER
	P@ssw0rd
	ENTER


vim openssl.cnf

[req]
req_extensions = req_ext

[req_ext]
subjectAltName = DNS:web.au-team.irpo, DNS:docker.au-team.irpo
extendedKeyUsage = serverAuth
keyUsage = digitalSignature

openssl x509 -req -in web.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
-out web.crt -days 365 -sha256 -extfile openssl.cnf -extensions req_ext

#_______________________________________ISP_____________________________________________

useradd sshuser
passwd sshuser
# P@ssw0rd
vim /etc/openssh/sshd_config
	Port 2026

systemctl enable --now sshd
systemctl restart sshd


#_______________________________________HQ-SRV__________________________________________

scp -P 2026 web.crt web.key sshuser@172.16.1.1:/home/sshuser/
	yes
	P@ssw0rd

#_______________________________________ISP_____________________________________________

mkdir /etc/nginx/ssl
# Перемещаем сертификат для HTTPS, который будет использовать Nginx
mv /home/sshuser/web.crt /etc/nginx/ssl/
# Перемещаем ключ для HTTPS, который будет использовать Nginx
mv /home/sshuser/web.key /etc/nginx/ssl/
chmod 777 /etc/nginx/ssl/web.key
vim /etc/nginx/sites-available.d/proxy.conf 

server {
    listen 80;
    server_name web.au-team.irpo docker.au-team.irpo;
    return 301 https://$host;
}
server {
    listen 443 ssl;
    server_name web.au-team.irpo;
    add_header Content-Security-Policy "upgrade-insecure-requests" always;
    ssl_certificate /etc/nginx/ssl/web.crt;
    ssl_certificate_key /etc/nginx/ssl/web.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    auth_basic "Restricted Access";
    auth_basic_user_file /etc/nginx/.htpasswd;
    location / {
        proxy_pass http://172.16.2.10:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
server {
    listen 443 ssl;
    server_name docker.au-team.irpo;
    add_header Content-Security-Policy "upgrade-insecure-requests" always;
    ssl_certificate /etc/nginx/ssl/web.crt;
    ssl_certificate_key /etc/nginx/ssl/web.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    location / {
        proxy_pass http://172.16.1.10:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

systemctl restart nginx

#_______________________________________HQ-CLI__________________________________________

su -
systemctl restart network	

#_______________________________________HQ-SRV__________________________________________


scp -P 2026 ca.crt sshuser@192.168.2.10:/tmp

#_______________________________________HQ-CLI__________________________________________

mkdir -p /etc/pki/ca-trust/source/anchors/
# Копируем корневой сертификат в системную папку доверенных УЦ
cp /tmp/ca.crt /etc/pki/ca-trust/source/anchors/au-team-ca.crt
# Обновляем базу доверенных сертификатов системы
update-ca-trust
trust list | grep -i "au-team"
