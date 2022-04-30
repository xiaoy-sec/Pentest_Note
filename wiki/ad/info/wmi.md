	>wmic OS get Caption,CSDVersion,OSArchitecture,Version系统版本
	>wmic service list brief 列出本机服务
	>wmic process list brief 列出进程
	>wmic process where name="chrome.exe" get executablepath进程路径
	>wmic process get caption,commandline /value>>1.txt查询所有进程参数
	>wmic process where caption="svchost.exe" get caption,commandline /value 查询某个进程命令行参数
	创建进程
	>wmic process call create calc
	>wmic process call create "C:\shell.exe"
	>wmic process call create "shutdown.exe -r -f -t 20"
	结束进程
	>wmic process where name="shell.exe" call terminate
	>wmic process where processid="2345" delete
	>wmic process 2345 call terminate
	>wmic startup list brief 列出自启动程序
	>wmic /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntiVirusProduct Get displayName /Format:List 查看杀毒软件
	>wmic netuse list brief 列出共享驱动盘
	>wmic ntdomain list brief 查询域控制器
	>wmic useraccount list brief 列出本机管理员及SID
	>wmic qfe list brief 列出补丁列表
	>wmic share get name,path 查看共享
	>wmic startup list brief查看启动项
	>wmic product get name,version 查看安装的软件
	>wmic product where "name like '%360%'" get name 查看程序名
	>wmic product where name="360tray" call uninstall 卸载程序
	>wmic process where "name like '%360%'" get name 查找进程全名
	>wmic product where name="360tray.exe" call terminate 停止程序
	>wmic desktop get screensaversecure,screensavertimeout 查看屏保