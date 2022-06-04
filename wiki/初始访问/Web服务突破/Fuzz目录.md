	>dirb http://192.168.0.1 /root/asp.txt,/root/dir.txt -a "USER-AGENT" –c "Cookie" -z 100
	>nikto -C all -h http://192.168.0.107 nikto扫描web服务
	>wpscan --url http://192.168.0.107/ -e u --wordlist /root/wordlist.txt 枚举用户爆破密码
	>wpscan --url http://192.168.0.107/ -e vp 扫描漏洞插件
	>perl joomscan.pl --url 192.168.0.107
  #### WFuzz
	爆破文件和文件夹
	>wfuzz -w wordlist URL/FUZZ.php
	>wfuzz -w wordlist URL/FUZZ
	枚举数字参数
	>wfuzz -z range,000-999 -b session=session -b cookie=cookie http://127.0.0.1/getuser.php?uid=FUZZ
	POST账号密码爆破FUZnZ
	>wfuzz -w userList -w pwdList -d "username=FUZZ&password=FUZ2Z" http://127.0.0.1/login.php
	随机HTTP头
	>wfuzz -z range,0000-9999 -H "X-Forwarded-For: FUZZ" http://127.0.0.1/get.php?userid=666
	使用代理fuzz
	>wfuzz -w wordlist -p 127.0.0.1:1087:SOCKS5 URL/FUZZ
	基础认证爆破
	>wfuzz -z list,"username-password" --basic FUZZ:FUZZ URL
	【结果过滤】--hc或--ss不显示符合条件的结果。
	【结果过滤】--sc或--sl或--sw或--sh显示符合条件的结果。
  #### Cewl
	爬行网站存为字典
	>cewl http://www.qq.com/ -w dict.txt
	指定字典长度
	>cewl http://www.qq.com/ -m 9 -w dict.txt
	网站提取Email
	>cewl http://www.qq.com/ -n –e
  #### Dirsearch
	-u 目标
	-e 文件扩展名
	-x 排除状态码结果
	-w 指定字段
	-r 递归扫描
	-R 设置递归级别
	-t 设置线程数（50-100）
	-c cookie
	>python3 dirsearch.py -u https://target
	>python3 dirsearch.py -e php,html,js -u https://target -w /path/to/wordlist
	>python3 dirsearch.py -e php,html,js -u https://target --proxy 127.0.0.1:8080
	>python3 dirsearch.py -e php,html,js -u https://target --proxy socks5://1.1.1.1:8080
	>python3 dirsearch.py --random-user-agents --recursive --thread 50 --extension php --plain-text-report report.txt –url http://127.0.0.1
  ####  dirmap
  	>python dirmap.py -i https://target.com -lcf
	>python dirmap.py -i 192.168.1.1 -lcf
	>python dirmap.py -i 192.168.1.0/24 -lcf
	>python dirmap.py -i 192.168.1.1-192.168.1.100 -lcf
	>python dirmap.py -iF targets.txt -lcf