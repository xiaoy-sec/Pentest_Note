	当获得了一个webshell的时候，下一步要反弹个shell回来
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/747.png)

	在尝试了https://github.com/trustedsec/unicorn独角兽失败之后，找到了一篇使用golang将shellcode注入到内存的文章
	https://labs.jumpsec.com/2019/06/20/bypassing-antivirus-with-golang-gopher-it/
	https://github.com/brimstone/go-shellcode
	https://golang.org/pkg/syscall/?GOOS=windows#NewLazyDLL
	该代码利用golang中的syscall包来调用NewLazyDLL  方法来加载Kernel32.dll，加载Kernel32.dll后，即可将其用于寻址和内存分配。编译后的代码将十六进制格式的msfvenom内容用作命令行参数。
	由于代码存在许久，可能直接使用会被检测到，这里对其进行了修改，重命名所有变量，通过URL方式加载shellcode，为了绕过沙盒，添加了一些其他的参数，如果不存在参数则退出执行。
	用powershell下载到服务器
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/748.png)

	等了几分钟，发现文件没有被删除，再执行。Msf收到会话
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/749.png)

	在尝试了getuid命令之后，返回了错误，查看了以下目录，还是被删除了
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/750.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/751.png)

	本地复现了下，可以看到被检测到了
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/752.png)

	绕过可以看一下微软的文章
	https://docs.microsoft.com/en-us/windows/security/threat-protection/microsoft-defender-antivirus/configure-server-exclusions-microsoft-defender-antivirus#list-of-automatic-exclusions
	Windows Server 2016和2019上的Microsoft Defender Antivirus自动将您注册为某些排除项，具体由您指定的服务器角色定义。请参阅  自动排除项列表 。这些排除项不会被windows defender检查。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/753.png)

	按照文章，创建个目录PHP5433，修改文件为php-cgi.exe即可绕过wd的防护
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/754.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/755.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/756.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/757.png)

	使用烂土豆提权
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/758.png)

	文中webshell: https://github.com/NetSPI/cmdsql
