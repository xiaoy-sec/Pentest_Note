  ##### 查看系统及版本
  	>cat /etc/issue
	>cat /etc/*-release
	>cat /etc/lsb-release      # Debian based
	>cat /etc/redhat-release   # Redhat based
	>cat /proc/version
	>uname -a
	>uname -mrs
	>rpm -q kernel
	>dmesg | grep Linux
	>ls /boot | grep vmlinuz-
  ##### 查看环境变量
	>cat /etc/profile
	>cat /etc/bashrc
	>cat ~/.bash_profile
	>cat ~/.bashrc
	>cat ~/.bash_logout
	>env
	>set
  ##### 查看应用/服务
  	>ps aux
	>ps -ef
	>top
	>cat /etc/services
	>ps aux | grep root
	>ps -ef | grep root
  ##### 查看安排的工作
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
  ##### 查看通讯/网络配置
  	>/sbin/ifconfig -a
	>cat /etc/network/interfaces
	>cat /etc/sysconfig/network
	>cat /etc/resolv.conf
	>cat /etc/sysconfig/network
	>cat /etc/networks
	>iptables -L
	>hostname
	>dnsdomainname
	>lsof -i
	>lsof -i :80
	>grep 80 /etc/services
	>netstat -antup
	>netstat -antpx
	>netstat -tulpn
	>chkconfig --list
	>chkconfig --list | grep 3:on
	>last
	>w
  ##### 嗅探/监听流量
  	>tcpdump tcp dst 192.168.1.7 80 and tcp dst 10.5.5.252 21
  ##### 查看日志文件
  	>cat /etc/httpd/logs/access_log
	>cat /etc/httpd/logs/access.log
	>cat /etc/httpd/logs/error_log
	>cat /etc/httpd/logs/error.log
	>cat /var/log/apache2/access_log
	>cat /var/log/apache2/access.log
	>cat /var/log/apache2/error_log
	>cat /var/log/apache2/error.log
	>cat /var/log/apache/access_log
	>cat /var/log/apache/access.log
	>cat /var/log/auth.log
	>cat /var/log/chttp.log
	>cat /var/log/cups/error_log
	>cat /var/log/dpkg.log
	>cat /var/log/faillog
	>cat /var/log/httpd/access_log
	>cat /var/log/httpd/access.log
	>cat /var/log/httpd/error_log
	>cat /var/log/httpd/error.log
	>cat /var/log/lastlog
	>cat /var/log/lighttpd/access.log
	>cat /var/log/lighttpd/error.log
	>cat /var/log/lighttpd/lighttpd.access.log
	>cat /var/log/lighttpd/lighttpd.error.log
	>cat /var/log/messages
	>cat /var/log/secure
	>cat /var/log/syslog
	>cat /var/log/wtmp
	>cat /var/log/xferlog
	>cat /var/log/yum.log
	>cat /var/run/utmp
	>cat /var/webmin/miniserv.log
	>cat /var/www/logs/access_log
	>cat /var/www/logs/access.log
	>ls -alh /var/lib/dhcp3/
	>ls -alh /var/log/postgresql/
	>ls -alh /var/log/proftpd/
	>ls -alh /var/log/samba/