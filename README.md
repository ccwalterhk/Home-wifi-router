# Home-wifi-router
This is to build home broadband / wifi router using Linux and raspberry Pi 


1. Enable DHCP on the LAN / wifi Interface


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

In dnsmasq.conf, 

interface=<the interface that DHCP to be enabled>
  dhcp-range=192.168.0.11,192.168.0.30,255.255.255.0,24h
