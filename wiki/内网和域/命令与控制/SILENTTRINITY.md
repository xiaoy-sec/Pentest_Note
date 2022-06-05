	https://github.com/byt3bl33d3r/SILENTTRINITY
	类似cobalt strike+empire的结合
	>git clone https://github.com/byt3bl33d3r/SILENTTRINITY
	>pip3 install --user pipenv && pipenv install && pipenv shell
	>python st.py
	服务端执行
	>python3 st.py teamserver <teamserver_ip> <teamserver_password>
	>python3 st.py teamserver 192.168.0.108 123456
	也可加参数--port指定端口
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/258.png)

	客户端执行
	>python3 st.py client wss://<username>:<teamserver_password>@<teamserver_ip>:5000
	>python3 st.py client wss://y:123456@192.168.0.108:5000
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/259.png)
	
	>listeners命令进入监听器目录
	>use http选择监听器
	>options命令查看需要配置的参数
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/260.png)

	>set Port 8081 使用set命令配置参数
	>start 启动监听器
	>list查看运行中的监听器
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/261.png)

	>stop http使用stop+监听器名字停止监听器
	>stagers进入payload目录
	>list列出可用payload
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/262.png)

	>use payloadname 命令use+payload名字
	>generate http generate+监听器名字生成payload
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/263.png)