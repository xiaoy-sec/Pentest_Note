	https://github.com/ShawnDEvans/smbmap
  #### MSF
	>use auxiliary/scanner/smb/smb_version查询开启139，445端口主机
	>use auxiliary/scanner/smb/smb_login 爆破
  #### NMAP
	>nmap -sU -sS --script smb-enum-shares.nse -p 445 192.168. 1.119
  #### CMD
	>for /l %a in (1,1,254) do start /min /low telnet 192.168.1.%a 445
