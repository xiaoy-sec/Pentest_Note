	建立连接
	>net use \\192.168.1.2\ipc$ "password" /user:domain\administrator
	查看连接
	>net use
	列文件
	>dir \\192.168.1.2\c$
	查看系统时间
	>net time \\192.168.1.2
	上传文件
	>copy 1.exe \\192.168.1.2\c$
	下载文件
	>copy \\192.168.1.2\c$\1.exe 1.exe
	批量IPC
	@echo off
	echo check ip addr config file…
	if not exist ip.txt echo ip addr config file ip.txt does not exist! & goto end
	echo read and analysis file…
	for /F "eol=#" %%i in (ip.txt) do start PsExec.exe \\%%i -accepteula -u administrator -p "123456" cmd & start cmd /c PsExec.exe \\%%i -u administrator -p "123456" cmd
	:end
	exit
