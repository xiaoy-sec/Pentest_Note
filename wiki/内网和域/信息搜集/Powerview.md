	获取当前域信息
	>powershell -exec bypass -Command "&{Import-Module .\powerview.ps1; Get-NetDomain}"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/302.png)

	>powershell -exec bypass -Command "&{Import-Module .\powerview.ps1; get-netforest}"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/303.png)

	枚举管理员
	>powershell -exec bypass -Command "&{Import-Module .\powerview.ps1; Invoke-EnumerateLocalAdmin}"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/304.png)

	查询管理在线的机器
	>powershell -exec bypass -Command "&{Import-Module .\powerview.ps1; invoke-userhunter}"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/305.png)

	查看域内机器以administrator权限运行的进程
	>powershell -exec bypass -Command "&{Import-Module .\powerview.ps1; invoke-processhunter }"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/306.png)

	或指定参数userfile和computerfile查询某台机器某个用户的进程
	>powershell -exec bypass -Command "&{Import-Module .\powerview.ps1; invoke-processhunter -Userfile .\user.txt -computerfile .\host.txt}"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/307.png)

	查询域内机器共享
	>powershell -exec bypass -Command "&{Import-Module .\powerview.ps1; Invoke-sharefinder}"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/308.png)

	查询域内机器
	>Get-NetComputer -Domain zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/309.png)

	>Find-LocalAdminAccess -verbose 查询域内本地用户能登录的机器
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/310.png)

	Dev-powerview
	获取域控机器和win版本
	>Get-DomainController |select name,osversion|fl 
	枚举其他域： Get-NetDomain -Domain <DomainName>
	获取域 SID： Get-DomainSID
	获取域策略：Get-DomainPolicy
	获取域控制器：
	>Get-NetDomainController
	>Get-NetDomainController -Domain <DomainName>
	枚举域用户：
	>Get-NetUser
	>Get-NetUser -SamAccountName <user> 
	>Get-NetUser | select cn
	检查上次修改密码
	>Get-UserProperty -Properties pwdlastset
	枚举域计算机
	>Get-NetComputer -FullData
	>Get-DomainGroup
	枚举存活主机
	>Get-NetComputer -Ping
	枚举组策略：Get-NetGPO
	显示某台机器的活动策略
	>Get-NetGPO -ComputerName <Name of the PC>
	枚举 OU：
	>Get-NetOU -FullData 
	>Get-NetGPO -GPOname <The GUID of the GPO>
	枚举域信任：
	>Get-NetDomainTrust
	>Get-NetDomainTrust -Domain <DomainName>
	查找当前域中当前用户具有本地管理员访问权限的所有计算机
	>Find-LocalAdminAccess -Verbose
	在域的所有机器上查找本地管理员
	>Invoke-EnumerateLocalAdmin -Verbose
	全部使用技巧
	https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993