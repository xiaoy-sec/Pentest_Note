	可以每天扫描，昨天安全不代表今天也安全
	https://github.com/haccer/subjack
	查看可能存在子域接管
	$ ./subjack -w <Subdomain List> -o results.txt -ssl -c fingerprints.json
	查看该域指向的位置
	$ dig <Domain Here>
	注意CNAME记录，可能就是我们需要注册接管的子域名
	https://github.com/EdOverflow/can-i-take-over-xyz