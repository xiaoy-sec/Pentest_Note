	1.将收集到的子域名保存，使用ping命令在内网循环
	for /f "delims=" %i in (host.txt) do @ping -w 1 -n 1 %i | findstr /c:"[10." /c:"[192." /c:"[172." >> C:/users/public/out.txt
	2.找到dns服务器ip，ipconfig或扫描开启53端口的机器
	https://github.com/Q2h1Cg/dnsbrute
	dnsbrute.exe -domain a.com -dict ziyuming.txt -rate 1000 -retry 1 -server 192.168.1.1:53
	3.扫描内网ip开启web服务的title
