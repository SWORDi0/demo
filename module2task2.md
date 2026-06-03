HQ-SRV 
mdadm --create /dev/md3 -l 5 -n 3 /dev/sdb /dev/sdc (third disk)
mdadm --detail --scan --verbose | tee -a /etc/mdadm.conf
mkfs.ext4 /dev/md3

vim /etc/fstab
/dev/md3 /raid ext4 defaults 0 0

mkdir /raid
mount -av

lsblk
