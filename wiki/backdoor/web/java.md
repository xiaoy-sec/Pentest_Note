  #### JAVA Web Backdoor
	From:https://www.freebuf.com/articles/web/172753.html
	https://github.com/rebeyond/memShell
	当获取一个webshell或bashshell权限时，下载后门执行注入进程形成无文件复活后门
	下载后解压到任意web目录
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/605.png)

	得到2个jar文件
	执行，password设置为你的密码
	>java -jar inject.jar password
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/606.png)

	注入成功，在web任意页面任意url执行命令
	http://192.168.0.121:8080/css/app.css?pass_the_world=password
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/607.png)

	可执行命令，反弹shell，上传/下载文件，列目录，读文件，添加代理，连接菜刀
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/608.png)
  #### Tomcat JSP HideShell
	From:https://mp.weixin.qq.com/s/7b3Fyu_K6ZRgKlp6RkdYoA
	https://github.com/QAX-A-Team/HideShell
	把自己的shell和hideshell传入靶机，先访问自己的shell，目的是为了让 Tomcat 将它编译，并生成 JspServletWrapper 保存在 JspRuntimeContext 中。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/609.png)

	再访问hideshell.jsp，点击hide你的shell。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/610.png)

	已经隐藏了
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/611.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/612.png)

	再访问hideshell.jsp，可以看到隐藏后的shell的文件名。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/613.png)

	访问看看
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/614.png)

	当然，也可以把hideshell自身隐藏了，那访问它的方式就是hidden-hideshell.jsp
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/615.png)

	目录里啥都没了
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/616.png)

	此方式隐藏之后请求不会产生日志
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/617.png)
	
	那如果把shelltest文件夹删掉权限还会在吗？
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/618.png)

	是在的
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/619.png)