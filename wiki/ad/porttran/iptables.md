	需开启ip转发功能
	>vim /etc/sysctl.conf设置net.ipv4.ip_forward=1
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/179.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/180.png)

	本地端口22转发到2222上
	>iptables -t nat -A PREROUTING -p tcp --dport 2222 -j REDIRECT --to-ports 22
	内网98机器3389转到本机110的6789上
	>iptables -t nat -A PREROUTING -d 192.168.0.110 -p tcp --dport 6789 -j DNAT --to-destination 192.168.0.98:3389
	>iptables -t nat -A POSTROUTING -d 192.168.0.98 -p tcp --dport 3389 -j SNAT --to 192.168.0.110
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/181.png)

	查看规则
	>iptables -t nat -L
	删除规则
	>iptables -t nat -D PREROUTING 1
	删除全部规则
	>iptables -t nat –F
