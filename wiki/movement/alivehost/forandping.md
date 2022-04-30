	>for /L %I in (1,1,254) DO @ping -w 1 -n 1 192.168.0.%I |findstr "TTL="
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/317.png)

	For+Ping命令查询域名对应IP
	>for /f "delims=" %i in (D:/domains.txt) do @ping -w 1 -n 1 %i | findstr /c:"[192." >> c:/windows/temp/ds.txt
