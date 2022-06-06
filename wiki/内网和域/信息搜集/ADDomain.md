	获取当前域： Get-ADDomain
	枚举其他域： Get-ADDomain -Identity <Domain>
	获取域 SID： Get-DomainSID
	获取域控制器：
	Get-ADDomainController
	Get-ADDomainController -Identity <DomainName>
	枚举域用户：
	Get-ADUser -Filter * -Identity <user> -Properties *
	枚举域计算机：
	Get-ADComputer -Filter * -Properties *
	Get-ADGroup -Filter * 
	枚举域信任：
	Get-ADTrust -Filter *
	Get-ADTrust -Identity <DomainName>
	枚举域林信任：
	Get-ADForest
	Get-ADForest -Identity <ForestName>
	枚举本地 AppLocker 有效策略：
	Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections