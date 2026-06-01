#_______________________________________HQ-SRV__________________________________________
apt-get update && apt-get install cups cups-pdf -y
vim /etc/cups/cupsd.conf

Listen 192.168.100.2:631

cupsctl --share-printers --remote-any
systemctl enable --now cups

systemctl status cups


#_______________________________________HQ-CLI__________________________________________

lpadmin -p HQ-PDF -E -v ipp://192.168.100.2/printers/Cups-PDF -m everywhere
lpadmin -d HQ-PDF

IN GUI find WIN then in "search" or "finder" search printer, press Print settings, print test page

#_______________________________________HQ-SRV__________________________________________


cp /var/spool/cups/d00001-001 /raid/nfs/Print.pdf

chmod 777 /raid/nfs/Print.pdf

#_______________________________________HQ-CLI__________________________________________

on workspace press home or maybe "home papka" > you need go to the / directory, find /mnt/nfs >  then put PDF on the workspace, bistrov need to seen in!
