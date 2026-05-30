#_______________________________________BR-SRV_______________________________________

vim import.sh

#!/bin/bash

mount -o loop /dev/sr0 /mnt

FILE="/mnt/Users.csv"

while IFS=";" read -r firstname lastname role phone ou street zip city country password; do
	password=$(echo "$password" | tr -d '[:space:]') 
	samba-tool user add "$firstname.$lastname" "$password"
done < <(tail -n +2 "$FILE")

save file

chmod +x import.sh
./import.sh
