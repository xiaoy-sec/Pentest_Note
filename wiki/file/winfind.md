	>cd /d E: && dir /b /s index.php
	>find /N /I "root" config.php
	>for /r E:\ %i in (config*.php) do @echo %i
	>for /r E:\ %i in (*.txt) do @echo %i
	>findstr /s /n "password" c:\*
	>powershell Get-ChildItem d:\ -Include index.php -recurse