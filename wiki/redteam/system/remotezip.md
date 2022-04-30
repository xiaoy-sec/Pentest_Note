	pip install remotezip
	#列出远程压缩包文件内容
	remotezip -l http://site/bigfile.zip
	#解压里面的文件
	remotezip "http://site/bigfile.zip" "file.txt"
