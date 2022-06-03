  ### windows
	在C:\Users\administrator\Desktop任意子目录  war.txt文件的同目录下
	写
	for /f %i in ('dir /s /b C:\Users\administrator\Desktop\war.txt') do (echo %i > %i\..\finddir.txt)
	删
	for /f %i in ('dir /s /b C:\Users\administrator\Desktop\war.txt') do (del %i\..\finddir.txt)
	在C:\Users\administrator\Desktop任意子目录  war文件夹下
	写
	for /f %i in ('dir /s /b C:\Users\administrator\Desktop\war') do (echo %i > %i\finddir.txt)
	删
	for /f %i in ('dir /s /b C:\Users\administrator\Desktop\war') do (del %i\finddir.txt)
  ### Linux
	在/root  war文件的同目录下
	写
	find /root -name war|while read file;do sh -c "echo $file">$(dirname $file)/finddir.txt;done
	删
	find /root -name war|while read file;do sh -c "rm $(dirname $file)/finddir.txt";done
	在/root  war文件夹下
	写
	find /root -name war|while read file;do sh -c "echo $file">$file/finddir.txt;done
	删
	find /root -name war|while read file;do sh -c "rm $file/finddir.txt";done