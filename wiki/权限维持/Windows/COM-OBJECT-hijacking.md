  #### CAccPropServicesClass and MMDeviceEnumerato
	无需超管权限，无需重启
	https://github.com/3gstudent/COM-Object-hijacking
	将恶意DLLbase64编码写入ps脚本
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/463.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/464.png)

	执行后会在
	%appdata%\Microsoft\Installer\{BCDE0395-E52F-467C-8E3D-C4579291692E}目录释放2个文件，分别是x86和x64的dll
	会在注册表中
	HKEY_CURRENT_USER\Software\Classes\CLSID\
	新建
	{b5f8350b-0548-48b1-a6ee-88bd00b4a5e7}和子项默认指向恶意DLL
	只要指向.net程序便可上线。如ie，mmc等
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/465.png)
  #### Explorer
	注册表位置：HKCU\Software\Classes\CLSID\
	创建项{42aedc87-2188-41fd-b9a3-0c966feabec1}
	创建子项InprocServer32
	Default的键值为恶意dll的绝对路径：C:\test\1.dll
	创建键值： ThreadingModel REG_SZ Apartment
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/466.png)

	HKCU\Software\Classes\CLSID{42aedc87-2188-41fd-b9a3-0c966feabec1}
	HKCU\Software\Classes\CLSID{fbeb8a05-beee-4442-804e-409d6c4515e9}
	HKCU\Software\Classes\CLSID{b5f8350b-0548-48b1-a6ee-88bd00b4a5e7}
	HKCU\Software\Classes\Wow6432Node\CLSID{BCDE0395-E52F-467C-8E3D-C4579291692E}