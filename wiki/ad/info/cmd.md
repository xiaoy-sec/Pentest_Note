	>whoami /user 查看当前用户SID
	>net config Workstation 查看当前计算机信息
	>net time /domain 判断主域
	错误5：存在域，当前不是域用户
	显示时间：存在域，当前是域内用户
	找不到域：不存在
	>net view /domain 列出域列表
	>net group "Domain Controllers" /domain查看主域控
	>nltest /DCLIST:zone.com 查看域控
	>net group "domain admins" /domain 查看域管理员
	>net group "enterprise admins" /domain 查看企业管理员列表
	>net localgroup administrators /domain 查看管理组用户
	>net group "domain computers" /domain 查看域成员计算机
	>net accounts /domain 查看密码策略
	>net user /domain查看域内用户
	>net view /domain:dc 查询域内计算机
	>netsh firewall set opmode disable/enable 关闭windows防火墙(win2003)
	>netsh advfirewall set allprofiles state off/on(大于win2003)
	>arp -a查看arp表
	>net start 查看服务
	>route print查看路由表
	>query user查看登录机器的用户的连接状态
	>tasklist /v 查看域管理员进程
	>dsquery server查询域控制器
	>dsquery computer 查询域内机器
	>dsquery user 查询域用户
	>dsquery ou 域内组织单位
	导出域DNS记录，文件保存在C:\Windows\System32\dns\
	>dnscmd /zoneexport zone.com 1.txt
	导出LDAP数据库
	>LDIFDE -f c:\windows\temp\dump.ldf -n -m