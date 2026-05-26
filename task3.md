HQ-SRV
apt-get install nfs-server -y

mkdir /raid/nfs
chmod -R 777 /raid/nfs
/etc/exports

/raid/nfs 192.168.200.0/28(rw,no_root_squash)
exportfs -arv
systemctl enable --now nfs-server

 HQ-CLI


mkdir /mnt/nfs
chmod -R 777 /mnt/nfs

/etc/fstab

192.168.100.2:/raid/nfs /mnt/nfs nfs defaults,_netdev 0 0
df -h 
