#_______________________________________HQ-SRV__________________________________________
apt-get update && apt-get install kernel-source-6.1 kernel-headers-modules-un-def gcc make kmod-sign -y
update-kernel
reboot
uname -r

Создать пользователя irpoadmin/P@ssw0rd
mysql -u root

SET PASSWORD FOR 'root'@'localhost' = PASSWORD('toor');
exit

mount /dev/sr0 /mnt
bash /mnt/cyberbackup_17.4.36200.x86_64
[*] Management Server -
[*] Agent for Linux 0
[ ] Bootable Media Builder a
[ ] Agent for CommuniGate Pro a
[*] Agent for MySQL/MariaDB a
ss -ltnp | grep 9877

#_______________________________________HQ-CLI__________________________________________

apt-get update && apt-get install kernel-source-6.1 kernel-headers-modules-un-def gcc make kmod-sign -y
update-kernel
reboot
uname -r

mount /dev/sr0 /mnt
bash /mnt/cyberbackup_17.4.36200.x86_64
[ ] Management Server -
[*] Agent for Linux 0
[ ] Bootable Media Builder a
[*] Storage Node a

В браузере: 192.168.1.10:9877

Настройки > Учетные записи > Создать Отдел > irpo

Нажать на отдел > Добавить учетную запись > irpoadmin > Enter > Нажать на плюсик > Принять

Планы > Защита > Создать план

Имя: etc_backup
Добавить устройства: hq-srv.au-team.irpo
Место хранения: Выбрать > Узел хранения > Обзор > Иконка гайки > Создать папку > backup > Дать имя "backup_dir" > Готово
# Запросит логин: root|toor
Выбор данных: Файлы/Папки: Указать > Выбрать файлы и папки > /etc/ > Готово

Имя: webdb_backup
Добавить устройства: hq-srv.au-team.irpo
Место хранения: backup_dir
Выбор данных: Вся машина
Резервное копирование приложения: MySQL/MariaDB > Добавить экземпляр:
Тип подключения: Сокет TCP(Первый вариант)
root|toor
# Вылезает ошибка о подключении
Тип подключения: Сокет Unix(Второй вариант)
Путь к сокету: /var/lib/mysql/mysql.sock
root|toor

Устройства > Создать резервную копию (hq-srv.au-team.irpo) > webdb_backup + etc_backup

Планы > Защита > webdb_backup + etc_backup(по одному) > Сведения > Развернуть > Запустить сейчас

