#_______________________________________HQ-SRV__________________________________________

apt-get update && apt-get install rsyslog logrotate -y
vim /etc/rsyslog.d/00_common.conf

# Раскоментить эти четыре строки
module(load="imudp") # needs to be done just once  
input(type="imudp" port="514")

module(load="imtcp") # needs to be done just once  
input(type="imtcp" port="514")

# В конце строки
$template RemoteLogs, "/opt/%HOSTNAME%.log"  
*.* ?RemoteLogs


systemctl enable --now rsyslog
systemctl restart rsyslog

#________________________________BR-SRV+HQ-RTR+BR-RTR__________________________________

apt-get update && apt-get install rsyslog logrotate rsyslog-journal -y
vim /etc/rsyslog.d/00_common.conf


#Раскоментить эти четыре строки
module(load="imjournal") # provides support for systemd-journald logging
module(load="imuxsock") # provides support for local system logging (e.g. via logger command)
module(load="imklog")    # provides kernel logging support (previously done by rklogd)
module(load="immark")    # provides --MARK-- message capability

# Найти строку "global..." и ввести как здесь.
global(workDirectory="/var/spool/rsyslog") # where to place spool files
*.warning @@192.168.1.10:514


#_______________________________________HQ-SRV__________________________________________

vim /etc/logrotate.d/rsyslog

/opt/*/*.log {
  weekly
  size 10M
  compress
  delaycompress
  missingok
  notifempty
  create 0640 root root
  sharedscripts
  postrotate
    /usr/bin/systemctl reload rsyslog > /dev/null 2>&1 || true
  endscript
}

export EDITOR=vim
crontab -e

# Используете TAB между пробелами.
0 0 * * 0 /usr/sbin/logrotate -f /etc/logrotate.d/rsyslog
