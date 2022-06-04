	>python2 -m SimpleHTTPServer 
	>python3 -m http.server 8080
	>php -S 0.0.0.0:8888
	>openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
	>openssl s_server -key key.pem -cert cert.pem -accept 443 –WWW
	>ruby -rwebrick -e "WEBrick::HTTPServer.new(:Port => 8888,:DocumentRoot => Dir.pwd).start"
	>ruby -run -e httpd . -p 8888