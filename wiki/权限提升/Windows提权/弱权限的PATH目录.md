	>for /f "tokens=2 delims='='" %a in ('wmic service list full^|find /i "pathname"^|find /i /v "system32"') do @echo %a >> c:\windows\temp\permissions.txt
	>for /f eol^=^"^ delims^=^" %a in (c:\windows\temp\permissions.txt) do cmd.exe /c icacls "%a"

	>sc query state=all | findstr "SERVICE_NAME:" >> Servicenames.txt
	>FOR /F %i in (Servicenames.txt) DO echo %i
	>type Servicenames.txt
	>FOR /F "tokens=2 delims= " %i in (Servicenames.txt) DO @echo %i >> services.txt
	>FOR /F %i in (services.txt) DO @sc qc %i | findstr "BINARY_PATH_NAME" >> path.txt
	或使用msf模块exploit/windows/local/service_permissions
	寻找BUILTIN\Users:(F)（完全访问）、BUILTIN\Users:(M)（修改访问）或 BUILTIN\Users:(W)（只写访问）
