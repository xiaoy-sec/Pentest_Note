	forfiles /p c:\windows\system32 /m notepad.exe /c <bin> 
	explorer.exe /root,"<bin>" 
	pcalua.exe -a <bin> 
	scriptrunner.exe -appvscript <bin> 
	wmic process call create <bin> 
	rundll32.exe advpack.dll, RegisterOCX <bin>
