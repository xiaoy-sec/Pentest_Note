	>use auxiliary/scanner/discovery/arp_sweep
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/318.png)

	>use auxiliary/scanner/discovery/udp_sweep
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/319.png)

	>use auxiliary/scanner/netbios/nbname
	meterpreter>run post/windows/gather/arp_scanner RHOSTS=192.168.1.1/24
	meterpreter>run post/multi/gather/ping_sweep RHOSTS=192.168.1.1/24
