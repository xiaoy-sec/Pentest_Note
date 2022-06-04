	360全套+火绒没有拦截
	缺点:会有黑框，并且打开chrome浏览器，功能限制
	https://github.com/0x09AL/Browser-C2
	>go get -u github.com/gorilla/mux
	>go get -u github.com/chzyer/readline
	>git clone https://github.com/0x09AL/Browser-C2.git
	/Browser-C2/agent/agent.go修改C2地址
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/264.png)

	修改chrome的位置
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/265.png)

	编译客户端
	>CGO_ENABLED=1 GOARCH= GOOS=windows go build
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/266.png)

	 /Browser-C2/static/jquery.js修改控制服务器IP
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/267.png)
	
	转到主目录编译服务器端
	>go build
	靶机执行生成好的客户端
	攻击机监听
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/268.png)

	此框架与靶机之间通信未加密，功能有限，可与msf、cs、poshc2、empire等框架建立联系。