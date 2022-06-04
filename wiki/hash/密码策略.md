	默认情况，主机账号的口令每30天变更一次
	>HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\Netlogon\Parameters，键值为DisablePasswordChange，设置为1，即表示禁止修改账号口令
	>组策略(gpedit.msc)中修改默认的30天，修改位置为"Computer Configuration\Windows Settings\Security Settings\Local Policies\Security Options\Domain member: Maximum machine account password age"设置为0时，表示无限长
	>禁止修改主机账号口令，用来支持VDI (virtual desktops)等类型的使用，具体位置为"Computer Configuration\Windows Settings\Security Settings\Local Policies\Security Options\Domain member: Disable machine account password changes"
	Debug Privilege
	本地安全策略>本地策略>用户权限分配>调试程序