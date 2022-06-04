	meterpreter > background
	msf > use auxiliary/server/socks4a
	再配置proxychains.conf
	>proxychains nmap -sT -sV -Pn -n -p22,80,135,139,445 --script=smb-vuln-ms08-067.nse 内网IP
