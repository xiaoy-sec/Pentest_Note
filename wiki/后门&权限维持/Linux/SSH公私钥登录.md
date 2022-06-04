	>vim /etc/ssh/sshd_conf取消以下注释
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/573.png)
	
	>ssh-keygen生成
	复制/root/.ssh/id_rsa.pub文件到攻击端的/root/.ssh/authorized_keys
	>ssh -i id_rsa targer@1.1.1.1