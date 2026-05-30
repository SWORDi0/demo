
#___________________________________HQ-RTR+BR-RTR_______________________________________

nft add table inet filter
nft add chain inet filter input { type filter hook input priority filter\; policy accept\; }
nft add chain inet filter forward { type filter hook forward priority filter\; policy accept\; }
nft add chain inet filter output { type filter hook output priority filter\; policy accept\; }
nft add rule inet filter input iif "lo" accept
nft add rule inet filter input iif "ens18" tcp dport { 22,53,80,443,2026,8080 } accept
nft add rule inet filter input iif "enp2s1" udp dport { 53,123,500,4500 } accept
nft add rule inet filter input iif "ens18" icmp type echo-request accept
nft add rule inet filter input meta l4proto esp accept
nft add rule inet filter input iif "enp2s1" ct state new drop
nft add rule inet filter forward meta l4proto esp accept
