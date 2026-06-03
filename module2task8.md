HQ-RTR

iptables -t nat -A PREROUTING -i ens18 -p tcp --dport 2013 -j DNAT --to-destination 192.168.100.2:2013
iptables -t nat -A PREROUTING -i ens18 -p tcp --dport 8083 -j DNAT --to-destination 192.168.100.2:80

iptables-save >> /etc/sysconfig/iptables

systemctl restart iptables

BR-RTR

iptables -t nat -A PREROUTING -i ens18 -p tcp --dport 2013 -j DNAT --to-destination 192.168.3.2:2013
iptables -t nat -A PREROUTING -i ens18 -p tcp --dport 8083 -j DNAT --to-destination 192.168.3.2:8083

iptables -t nat -L -n -v

for delete the wrong rools you need to:

iptables -t nat -D PREROUTING 1
