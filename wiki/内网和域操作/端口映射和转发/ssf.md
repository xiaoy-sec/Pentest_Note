  #### 单层网络正向转发
	https://github.com/securesocketfunneling/ssf/releases
	内网机执行：
	>ssfd.exe -p 1080
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/170.png)

	边界机器执行
	>ssf.exe -L 12138:10.1.1.108:22 -p 1080 192.168.0.98 
	把内网10.1.1.108的SSH转发出来
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/171.png)

	边界机器访问内网端口
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/172.png)	
  #### 单层网络反向转发
	边界机器执行：
	>ssfd.exe -p 1080
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/173.png)	

	内网机器执行：
	>ssf.exe -R 12138:10.1.1.108:22 -p 1080 192.168.0.106
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/174.png)	
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/175.png)
