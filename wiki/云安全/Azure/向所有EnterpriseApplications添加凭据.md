	添加
	PS > . C:\Tools\Add-AzADAppSecret.ps1
	PS > Add-AzADAppSecret -GraphToken $graphtoken -Verbose

	使用密钥作为服务主体进行身份验证
	PS > $password = ConvertTo-SecureString '<SECRET/PASSWORD>' -AsPlainText -Force
	PS > $creds = New-Object System.Management.Automation.PSCredential('<AppID>', $password)
	PS > Connect-AzAccount -ServicePrincipal -Credential $creds -Tenant '<TenantID>'