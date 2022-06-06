  #### AzureHound
	>. .\AzureHound.ps1
	>Invoke-AzureHound
  #### BloodHound
	>.\SharpHound.exe -c all -d active.htb --searchforest
	搜集不包括GPOLocalGroup的信息
	>.\SharpHound.exe -c all,GPOLocalGroup
	只从DC手机信息
	>.\SharpHound.exe --CollectionMethod DCOnly
	>.\SharpHound.exe -c all --LdapUsername <UserName> --LdapPassword <Password> --JSONFolder <PathToFile>
	>.\SharpHound.exe -c all --LdapUsername <UserName> --LdapPassword <Password> --domaincontroller 10.10.10.100 -d active.htb
	>.\SharpHound.exe -c all,GPOLocalGroup --outputdirectory C:\Windows\Temp --randomizefilenames --prettyjson --nosavecache --encryptzip --collectallproperties --throttle 10000 --jitter 23
	或使用powershell
	https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1
	/usr/lib/bloodhound/resources/app/Collectors/SharpHound.ps1
	>Invoke-BloodHound -SearchForest -CSVFolder C:\Users\Public
	>Invoke-BloodHound -CollectionMethod All  -LDAPUser <UserName> -LDAPPass <Password> -OutputDirectory <PathToFile>
	或远程使用BloodHound Python
	https://github.com/fox-it/BloodHound.py
	>pip install bloodhound
	>bloodhound-python -d lab.local -u rsmith -p Winter2017 -gc LAB2008DC01.lab.local -c all
	使用 Certipy 收集更多用于证书利用的数据
	>certipy find 'corp.local/john:Passw0rd@dc.corp.local' -bloodhound
	然后将 zip/json 文件导入 Neo4J 数据库并进行查询。
	>apt install bloodhound 
	启动BloodHound和数据库
	>neo4j console
	或者使用docker
	>docker run -p7474:7474 -p7687:7687 -e NEO4J_AUTH=neo4j/bloodhound neo4j
	>./bloodhound --no-sandbox
	访问http://127.0.0.1:7474  user:neo4J, pass:neo4j