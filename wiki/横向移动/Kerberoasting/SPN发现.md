  #### cmd
	>setspn -T 域名 -Q */*
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/375.png)
  #### Powershell
	https://github.com/PyroTek3/PowerShell-AD-Recon
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/376.png)

	Powerview
	>Get-NetComputer -SPN termsrv*
	>Get-NetUser -SPN
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/377.png)

	>import module GetUserSPNs.ps1
  #### Empire
	>usemodule situational_awareness/network/get_spn
