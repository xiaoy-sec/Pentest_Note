	如果在 CA 上设置了此标志，则任何请求（包括从 Active Directory 构建主题时）都可以在主题备用名称中包含用户定义的值。
	https://github.com/GhostPack/Certify
	用Certify.exe检查引用该标志的UserSpecifiedSAN标志状态EDITF_ATTRIBUTESUBJECTALTNAME2。
	>Certify.exe cas
	为模板申请证书并添加替代名称，即使默认User模板通常不允许指定替代名称
	>.\Certify.exe request /ca:dc.domain.local\domain-DC-CA /template:User /altname:DomAdmin