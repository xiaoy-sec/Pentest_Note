	keyvault 访问令牌
	curl "$IDENTITY_ENDPOINT?resource=https://vault.azure.net&apiversion=2017-09-01" -H secret:$IDENTITY_HEADER
	curl "$IDENTITY_ENDPOINT?resource=https://management.azure.com&apiversion=2017-09-01" -H secret:$IDENTITY_HEADER

	连接
	PS> $token = 'eyJ0..'
	PS> $keyvaulttoken = 'eyJ0..'
	PS Az> Connect-AzAccount -AccessToken $token -AccountId 2e91a4fea0f2-46ee-8214-fa2ff6aa9abc -KeyVaultAccessToken $keyvaulttoken

	查询vault和密钥
	PS Az> Get-AzKeyVault
	PS Az> Get-AzKeyVaultSecret -VaultName ResearchKeyVault
	PS Az> Get-AzKeyVaultSecret -VaultName ResearchKeyVault -Name Reader -AsPlainText