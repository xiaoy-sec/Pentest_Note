	GPO
	HKLM\SOFTWARE\Policies\Microsoft\Windows\SrpV2（keys：Appx、Dll、Exe、Msi 和脚本）。
	列出 AppLocker 规则
	PowerView PS C:\> Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
	AppLocker 绕过
	默认情况下，C:\Windows不被阻止，C:\Windows\Tasks任何用户都可以写
	https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/Generic-AppLockerbypasses.md
	https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/VerifiedAppLockerBypasses.md
	https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/DLL-Execution.md