	https://github.com/morph3/Windows-RPC-Backdoor
	生成头文件
	>midl /app_config rpc_backdoor_server.idl
	编译服务器，
	>cl.exe .\server.cpp .\rpc_backdoor_server_s.c
	运行服务器，
	>.\server.exe
	编译客户端，
	>cl.exe .\client.cpp .\rpc_backdoor_server_c.c
	运行客户端，
	>.\client.exe ipconfig
	或者
	>python3 client.py whoami