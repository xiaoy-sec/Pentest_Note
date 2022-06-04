	使用inline_syscall内联注入shellcode，结合sgn，donut等项目，增加了一些反调试技巧
	https://github.com/phra/PEzor
	$ git clone https://github.com/phra/PEzor.git 
	$ cd PEzor 
	$ sudo bash install.sh 
	$ bash PEzor.sh –h
	这里测试下mimikatz，-sleep设置为2分钟，执行后需等两分钟
	打包之前
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/738.png)

	打包之后
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/739.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/740.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/741.png)

	测试下Covenant
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/742.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/743.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/744.png)
