sudo iptables -I INPUT -p tcp -s 192.168.8.100/32 -d 192.168.8.106 --dport 8000 -j ACCEPT
sudo iptables -A INPUT -p tcp -s 0.0.0.0/0 --dport 8000 -j DROP
