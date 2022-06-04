	利用场景：
	激活guest修改rid为管理员的
	修改低权限用户rid
	劫持rid之前普通用户1的rid值
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/450.png)

	使用msf的post/windows/manage/rid_hijack模块
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/451.png)

	运行后可以看到已经变为超管的rid值
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/452.png)

	此时普通用户1登录系统是为超管权限