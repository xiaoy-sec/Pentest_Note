	安装strace
	>apt-get install strace
	>vi ~/.bashrc
	添加
	alias ssh='strace -o /tmp/.log -e read,write,connect -s 2048 ssh'