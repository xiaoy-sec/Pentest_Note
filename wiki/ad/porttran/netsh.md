	边界机器执行：
	>netsh interface portproxy add v4tov4 listenaddress=192.168.0.98 listenport=2222 connectaddress=10.1.1.108 connectport=22
	将内网10.1.1.108主机22端口转发至本机2222端口，攻击机连接边界机器2222端口即可访问内网SSH
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/176.png)

	>netsh interface portproxy add v4tov4 listenaddress=192.168.0.98 listenport=13389 connectaddress=192.168.0.98 connectport=3389
	当靶机某服务只允许内网访问时，将端口转发出来
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/177.png)

	添加防火墙规则：
	>netsh advfirewall firewall add rule name="RDP" protocol=TCP dir=in localip=192.168.0.98 localport=13389 action=allow
	列出所有转发规则:
	>netsh interface portproxy show all
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/178.png)

	删除指定的端口转发规则：
	>netsh interface portproxy delete v4tov4 listenport=13389 listenaddress=192.168.0.98
	删除所有转发规则：
	>netsh interface portproxy reset
