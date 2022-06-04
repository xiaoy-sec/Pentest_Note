	$sudo apt-get install clang git gcc make libpcap-dev
	$git clone https://github.com/robertdavidgraham/masscan
	$cd masscan
	$make 
	>masscan -p80,3389,1-65535 192.168.0.0/24
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/322.png)
