	https://gist.github.com/b4rtik/48ef702603d5e283bc81a05a01fccd40
	现amsi已经集成到win10以下组件中
	UAC
	PowerShell
	Windows脚本（wscript.exe和cscript.exe）
	JavaScript和VBScript
	Office VBA宏
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/721.png)

	这里使用nc来反弹个shell
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/722.png)

	使用regsvr32注册dll或手动添加
	HKEY_LOCAL_MACHINE\SOFTWARE\Classes\CLSID\GUID（默认）REG_SZ “提供程序描述”
	HKEY_LOCAL_MACHINE\SOFTWARE\Classes\CLSID\GUID\InprocServer32 (默认)
	REG_EXPAND_SZ " DLL的路径" -ThreadingModel REG_SZ "Both"
	HKLM \ SOFTWARE \ Microsoft \ AMSI \ Providers \ GUID
	Regsvr32使用超管权限
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/723.png)

	一旦注册，Dll将被加载到任何涉及AMSI和SampleAmsiProvider::Scan方法的进程中，比如在程序中设定，在powershell下发送字符串，触发scan方法，当发送字符串为我们设定的字符串的时候就触发恶意DLL
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/724.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/725.png)