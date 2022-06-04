	>psexec –accepteula @ips.txt –u admin –p pass@123 –c 1.bat
	#1.bat内容
	tasklist /v | find “域管理名字”
	@echo off
	echo check ip addr config file…
	if not exist ip.txt echo ip addr config file ip.txt does not exist! & goto end
	echo read and analysis file…
	for /F “eol=#” %%i in (ip.txt) do echo %%i &(echo %%i &tasklist /s %%i /u administrator /p pass@123 /v) >>d:\result.txt
	:end
	exit
