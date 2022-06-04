	>chattr +I shell.sh
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/581.png)

	>vim .shell.sh
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/582.png)

	>attrib +s +h +r 1.txt
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/583.png)
	
	>touch -r 1.file 2.file 修改2file文件的时间跟1file时间相同