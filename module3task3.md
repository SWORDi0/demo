#_______________________________________HQ-RTR__________________________________________

apt-get update
apt-get install strongswan iproute2 -y

modprobe ip_gre
modprobe af_key
modprobe xfrm_user

vim /etc/strongswan/swanctl/swanctl.conf

connections {
    hq-to-br {
        local_addrs = 172.16.1.10
        remote_addrs = 172.16.2.10

        local {
            auth = psk
            id = 172.16.1.10
        }
        remote {
            auth = psk
            id = 172.16.2.10
        }

        children {
            gre-traffic {
                local_ts = dynamic[gre]
                remote_ts = dynamic[gre]
                mode = transport
                esp_proposals = aes256-sha256-modp2048
                start_action = start
            }
        }

        version = 2
        proposals = aes256-sha256-modp2048
        reauth_time = 28800s
    }
}

secrets {
    ike-hq-to-br {
        id = 172.16.1.10
        id = 172.16.2.10
        secret = "123qweR%"
    }
}


chmod 600 /etc/strongswan/swanctl/swanctl.conf
systemctl enable ipsec
systemctl start ipsec
swanctl --load-all
vtysh

conf t
interface gre1
 ip ospf cost 100
 ip ospf hello-interval 10
 ip ospf dead-interval 40
 end
 write

#_______________________________________BR-RTR__________________________________________

apt-get update
apt-get install strongswan iproute2 -y

modprobe ip_gre
modprobe af_key
modprobe xfrm_user

vim /etc/strongswan/swanctl/swanctl.conf

connections {
    hq-to-br {
        local_addrs = 172.16.2.10
        remote_addrs = 172.16.1.10
        local {
            auth = psk
            id = 172.16.2.10
        }
        remote {
            auth = psk
            id = 172.16.1.10
        }
        children {
            gre-traffic {
                local_ts = dynamic[gre]
                remote_ts = dynamic[gre]
                mode = transport
                esp_proposals = aes256-sha256-modp2048
                start_action = start
            }
        }
        version = 2
        proposals = aes256-sha256-modp2048
        reauth_time = 28800s
    }
}

secrets {
    ike-hq-to-br {
        id = 172.16.2.10
        id = 172.16.1.10
        secret = "123qweR%"
    }
}

chmod 600 /etc/strongswan/swanctl/swanctl.conf
systemctl enable ipsec
systemctl start ipsec
swanctl --load-all
vtysh

conf t
interface gre1
 ip ospf cost 100
 ip ospf hello-interval 10
 ip ospf dead-interval 40
 end
 write
