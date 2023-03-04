# Home-wifi-router
This is to build home broadband / wifi router using Linux and raspberry Pi 



1. enable IP forwarding

```
sudo nano /etc/sysctl.conf
Now find this line:
#net.ipv4.ip_forward=1
…and delete the “#” – leaving the rest, so it just reads:
net.ipv4.ip_forward=1
```


2. enable wifi as hotspot

```
=> sudo apt install hostapd

=> edit /etc/hostapd/hostapd.conf

country_code=SG
interface=wlan0
ssid=Resp_wifi
hw_mode=g
channel=7
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=<password>
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```

3. Enable the default gateway interface

```
=> edit /etc/netplan/50-cloud-init.yaml  and use “netplan apply” to apply. 

network:
    ethernets:
        wlan0:
            addresses: [192.168.2.1/24]
            dhcp4: no
            nameservers:
                    addresses: [8.8.8.8, 8.8.4.4]
            optional: true 
    version: 2
    
```    

4. Enable DHCP on the LAN / wifi Interface

```
=> Create etc/resolv.conf for DNS resolution 
nameserver 8.8.8.8

=> Disable systems-resolved 
sudo systemctl disable systemd-resolved
sudo systemctl mask systemd-resolved
sudo systemctl stop systemd-resolved

=> Install dnsmasq and config DHCP
apt install dnsmasq 
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
sudo nano /etc/dnsmasq.conf

=> In dnsmasq.conf, 

interface=<the interface that DHCP to be enabled>
  dhcp-range=192.168.0.11,192.168.0.30,255.255.255.0,24h
  ## 192.168.0.11 - 192.168.0.30 is the DHCP range
```


5. Create iptables rule file

```
edit  /etc/iptables/rules.v4

*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [18:504]
-A INPUT -i lo -j ACCEPT
-A INPUT -i <internal interface> -j ACCEPT
-A INPUT -i <external interface> -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i <internal>  -o <external> -j ACCEPT
-A FORWARD -i <external> -o <internal> -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
COMMIT

*nat
:PREROUTING ACCEPT [1316:308333]
:INPUT ACCEPT [316:16806]
:OUTPUT ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]
-A POSTROUTING -o <external> -j MASQUERADE
COMMIT

```



