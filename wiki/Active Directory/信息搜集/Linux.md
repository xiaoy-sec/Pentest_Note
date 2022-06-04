	操作系统&内核版本&环境变量
	>cat /etc/issue
	>cat /etc/*-release
	>cat /etc/lsb-release
	>cat /etc/redhat-release
	cat /proc/version
	>uname -a
	>uname -mrs
	>rpm -q kernel
	>dmesg | grep Linux
	>ls /boot | grep vmlinuz-
	>cat /etc/profile
	>cat /etc/bashrc
	>cat ~/.bash_profile
	>cat ~/.bashrc
	>cat ~/.bash_logout
	>env
	>set
	Root权限进程
	>ps aux | grep root
	>ps -ef | grep root
	计划任务
	>crontab -l
	>ls -alh /var/spool/cron
	>ls -al /etc/ | grep cron
	>ls -al /etc/cron*
	>cat /etc/cron*
	>cat /etc/at.allow
	>cat /etc/at.deny
	>cat /etc/cron.allow
	>cat /etc/cron.deny
	>cat /etc/crontab
	>cat /etc/anacrontab
	>cat /var/spool/cron/crontabs/root
	IP信息
	>/sbin/ifconfig -a
	>cat /etc/network/interfaces
	>cat /etc/sysconfig/network
	连接信息
	>grep 80 /etc/services
	>netstat -antup
	>netstat -antpx
	>netstat -tulpn
	>chkconfig --list
	>chkconfig --list | grep 3:on
	>last
	>w
	用户信息
	>id
	>whomi
	>w
	>last
	>cat /etc/passwd
	>cat /etc/group
	>cat /etc/shadow
	>ls -alh /var/mail/
	>grep -v -E "^#" /etc/passwd | awk -F: '$3 == 0 { print $1}'   # 列出超级用户
	>awk -F: '($3 == "0") {print}' /etc/passwd   #列出超级用户
	>cat /etc/sudoers
	>sudo –l
	操作记录
	>cat ~/.bash_history
	>cat ~/.nano_history
	>cat ~/.atftp_history
	>cat ~/.mysql_history
	>cat ~/.php_history
	可写目录
	>find / -writable -type d 2>/dev/null      # 可写目录
	>find / -perm -222 -type d 2>/dev/null     # 可写目录 
	>find / -perm -o w -type d 2>/dev/null     # 可写目录
	>find / -perm -o x -type d 2>/dev/null     # 可执行目录
	>find / \( -perm -o w -perm -o x \) -type d 2>/dev/null   # 可写可执行目录