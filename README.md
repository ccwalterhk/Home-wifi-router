# Home-wifi-router
This is to build home broadband / wifi router using Linux and raspberry Pi 


1. Enable DHCP on the LAN / wifi Interface

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

2. enable IP forwarding

```
sudo nano /etc/sysctl.conf
Now find this line:
#net.ipv4.ip_forward=1
…and delete the “#” – leaving the rest, so it just reads:
net.ipv4.ip_forward=1
```

3. enable wifi as hotspot

=> sudo apt install hostapd

=> edit /etc/hostapd/hostapd.conf

```
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
