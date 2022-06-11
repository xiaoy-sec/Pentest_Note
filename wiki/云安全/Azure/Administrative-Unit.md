	Administrative Unit可以重置其他用户密码

	PS AzureAD> Get-AzureADMSAdministrativeUnit -Id <ID>
	PS AzureAD> Get-AzureADMSAdministrativeUnitMember -Id <ID>
	PS AzureAD> Get-AzureADMSScopedRoleMembership -Id <ID> | fl
	PS AzureAD> Get-AzureADDirectoryRole -ObjectId <RoleId>
	PS AzureAD> Get-AzureADUser -ObjectId <RoleMemberInfo.Id> | fl 
	PS C:\Tools> $password = "Password" | ConvertToSecureString -AsPlainText -Force
	PS C:\Tools> (Get-AzureADUser -All $true | ?{$_.UserPrincipalName -eq "<Username>@<TENANT NAME>.onmicrosoft.com"}).ObjectId | SetAzureADUserPassword -Password $Password -Verbose
