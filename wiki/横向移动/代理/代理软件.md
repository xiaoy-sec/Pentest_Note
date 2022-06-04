	Sockscap64
	Proxifier 
	Proxychains
	>vim /etc/proxychains.conf
	去掉dynamic_chain注释>添加socks4 127.0.0.1 1080
	>cp /usr/lib/proxychains3/proxyresolv /usr/bin
