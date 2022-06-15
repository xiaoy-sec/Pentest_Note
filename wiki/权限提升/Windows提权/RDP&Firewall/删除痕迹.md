	@echo off
	reg delete "HKEY_CURRENT_USER\Software\Microsoft\Terminal Server Client\Default" /va /f
	reg delete "HKEY_CURRENT_USER\Software\Microsoft\Terminal Server Client\Servers" /f
	reg add "HKEY_CURRENT_USER\Software\Microsoft\Terminal Server Client\Servers"
	cd %userprofile%\documents\attrib Default.rdp -s -h
	del Default.rdp
	以上保存bat文件执行