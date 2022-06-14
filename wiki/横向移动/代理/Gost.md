	>git clone https://github.com/ginuerzh/gost
	>cd gost/cmd/gost
	>go build
	Socks5 Proxy
	>Server side: gost -L=socks5://:1080
	>Client side: gost -L=:8080 -F=socks5://server_ip:1080?notls=true

	端口转发
	>gost -L=tcp://:2222/192.168.1.1:22 [-F=..]