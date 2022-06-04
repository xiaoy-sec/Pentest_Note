	● Virus Total
	● Netcraft
	● DNSdumpster
	● Threat crowed
	● Shodan
	● Cencys
	● DNSdb
	● Pastebin
	http://chaos.projectdiscovery.io/
	老站、同样架构或同源码的子站
	爆破，接口查询
	  https://phpinfo.me/domain/
	  https://d.chinacycc.com/index.php?m=Login&a=index
	  subDomainBrute、knockpy
	OWA发现、dig adfs、dig mail
	https://linux.die.net/man/1/host 
	https://dns.bufferover.run/dns?q=baidu.com
	http://api.hackertarget.com/reversedns/?q=target.com
	https://github.com/OJ/gobuster
	  $ ./gobuster dns -d xxx.com -w subdomains.txt
	https://github.com/infosec-au/altdns
	  $ altdns -i found_subdomains.txt -o permutation_output -w words.txt -r -s resolved_output.txt
	https://github.com/OWASP/Amass
	  $ amass enum -passive -d <Domain Name Here>
	https://github.com/guelfoweb/knock
	  $ knockpy.py <Domain Name Here>
	SecurityTrails API查询子域名
	  去https://securitytrails.com/申请个免费的API
	  curl -s --request GET --url https://api.securitytrails.com/v1/domain/target.com/subdomains?apikey=API_KEY | jq '.subdomains[]' | sed 's/\"//g' >test.txt 2>/dev/null && sed "s/$/.target.com/" test.txt | sed 's/ //g' && rm test.txt
	![image](../../../img/715.png)
	subfinder - Subfinder 是一个子域发现工具，可以为网站发现有效的子域。 
	https://github.com/projectdiscovery/subfinder 
	  >subfinder -d google.com -all -v 
	assetfinder - 查找与给定域相关的域和子域。 
	https://github.com/tomnomnom/assetfinder 
	  >assetfinder --subs-only google.com
	knockknock - 一个简单的反向 whois 查找工具，它返回个人或公司拥有的域列表。 
	https://github.com/harleo/knockknock 
	  >knockknock -n google.com -p 
	findomain - 域识别的完整解决方案。支持截屏、端口扫描、HTTP 检查、从其他工具导入数据、子域监控、通过 Discord、Slack 和 Telegram 发出警报、用于源的多个 API 密钥等等。 
	https://github.com/Findomain/Findomain 
	  >findomain -t google.com 
	hakrevdns - 用于执行大量反向 DNS 查找的小型快速工具。 
	https://github.com/hakluke/hakrevdns 
	  >prips 173.0.84.0/24 | hakrevdns -d 
	googlecertfarm
	https://github.com/rook1337/googlecertfarm 
	  >python3 googlecertfarm.py -d google.com
	https://github.com/shmilylty/OneForAll
	  >python oneforall.py --target example.com run
	  >python oneforall.py --targets ./example.txt run