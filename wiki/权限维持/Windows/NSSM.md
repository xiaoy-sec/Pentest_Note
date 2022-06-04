	http://www.nssm.cc/release/nssm-2.24.zip
	NSSM封装可执行程序为系统服务
	>nssm install 服务名称会自动弹出设置
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/512.png)

	Path选择powershell的路径，arguments直接输入参数。
	启动服务
	>nssm start 服务名称
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/513.png)

	会上线
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/514.png)

	重启电脑，权限也会维持
	删除服务
	>nssm remove <servicename>
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/515.png)