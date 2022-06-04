	https://dnsdumpster.com/
	https://censys.io/
	https://dnsdb.io/zh-cn
	https://hackertarget.com/find-dns-host-records
	https://securitytrails.com/
	域传送漏洞检查
	  Dnsenum、fierce，dnsrecon
	http://ha.ckers.org/fierce/ 
	$ ./fierce.pl -dns example.com 
	$ ./fierce.pl –dns example.com –wordlist myWordList.txt
	>dig @ns.example.com example=.com AXFR 
	>nslookup -type=ns xxx.yyy.cn #查询解析某域名的DNS服务器
	>nslookup #进入nslookup交互模式
	>server dns.domian.com #指定dns服务器
	>ls xxx.yyy.cn #列出域信息
	https://github.com/fwaeytens/dnsenum 
	dns-brute-script - Nmap 将尝试通过暴力破解流行的子域名来枚举 DNS 主机名。 