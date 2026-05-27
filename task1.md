hq-srv
apt-get install task-samba-dc -y

rm -f  /etc/samba/smb.conf
rm -rf /var/lib/samba/
rm -rf /var/cache/samba/
mkdir -p /var/lib/samba/sysvol

/etc/net/ifaces/ens18/resolv.conf

search au-team.irpo
nameserver 127.0.0.1

systemctl restart network

samba-tool domain provision

Administrator password: P@ssw0rd

retype

cp /var/lib/samba/private/krb5.conf /etc/krb5.conf

overwrite: y

 systemctl enable --now samba

 samba-tool group add hq

 for i in {1..5}; do
>samba-tool user add hquser$i P@ssw0rd;
>samba-tool user setexpiry hquser$i --noexpiry;
>samba-tool group addmembers "hq" hquser$i;
>done

  hq-rtr
  
vim /etc/dhcp/dhcpd.conf

change
option domain-name-servers      192.168.3.2; 

systemctl restart dhcpd

   hq-cli
   

systemctl restart network

Enter the AD, then restart machine

control sudo public
vim /etc/sudoers

commit

%hq ALL=(root) /usr/bin/id, /bin/grep, /bin/cat
