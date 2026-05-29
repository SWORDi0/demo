ISP
apt-get install chrony -y
vim /etc/chrony.conf

local stratum 5
komment
#pool pool.ntp.org iburst 

in Chrony.conf

server ntp0.ntp-servers.net iburst prefer minstratum 4
allow 0.0.0.0/0

systemctl restart chronyd


apt-get update && apt-get install chrony -y
/etc/chrony.conf
komment
#pool pool.ntp.org iburst 
enter
server 172.16.1.1 iburst

systemctl restart chronyd

for HQ-SRV HQ-CLI HQ-RTR there is 172.16.1.1 iburst
FOR BR-SRV BR-RTR there is 172.16.2.1 iburst

on kli chronyc sources

on server chronyc clients
