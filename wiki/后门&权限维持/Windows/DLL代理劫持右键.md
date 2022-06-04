	右键对应的注册表路径是
	HKLM\Software\Classes\*\ShellEx\ContextMenuHandlers
	使用autoruns查看加载的DLL
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/474.png)

	以rarext.dll为例
	使用https://github.com/rek7/dll-hijacking创建代理DLL
	注意修改parse.py中dumpbin.exe的位置
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/475.png)

	>python3 parse.py -d rarext.dll
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/476.png)

	修改原DLL为rarext_.dll，重新生成解决方案命名为rarext.dll
	将两个DLL放入原目录，重启