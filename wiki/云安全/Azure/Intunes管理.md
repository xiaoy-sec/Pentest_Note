	要求：
	全局管理员或Intune管理员特权：Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

	登录https://endpoint.microsoft.com/#home或使用 Pass-The-PRT
	转到设备->所有设备以检查注册到 Intune 的设备
	转到脚本并单击添加Windows 10。
	添加Powershell 脚本
	在分配页面中指定添加所有用户和添加所有设备。
	执行脚本最多需要一小时