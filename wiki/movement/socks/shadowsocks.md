	https://github.com/shadowsocks/libQtShadowsocks/releases/download/v2.0.2/shadowsocks-libqss-v2.0.2-win64.7z
	靶机新建配置文件1.json，内容为
	{
	"server":"0.0.0.0",
	"server_port":13337,
	"local_address":"127.0.0.1",
	"local_port":1080,
	"password":"123456",
	"timeout":300,
	"method":"aes-256-cfb",
	"fast_open":false,
	"workers": 1
	}
	执行
	>shadowsocks-libqss.exe -c 1.json –S
	攻击机配置
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/359.png)

	浏览器或其他攻击软件配置代理127.0.0.1:1080即可(需有http(s)/socks5功能)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/360.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/361.png)
