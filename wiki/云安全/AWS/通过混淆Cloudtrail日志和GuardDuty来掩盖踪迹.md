	在 Kali Linux、Pentoo 和 Parrot Linux 上使用 awscli 时，会根据用户代理生成日志。

	Pacu 通过定义自定义用户代理绕过这个问题（https://github.com/RhinoSecurityLabs/pacu/blob/master/pacu.py#L1473）

	boto3_session = boto3.session.Session()
	ua = boto3_session._session.user_agent()
	if 'kali' in ua.lower() or 'parrot' in ua.lower() or 'pentoo' in ua.lower():  # If the local OS is Kali/Parrot/Pentoo Linux
	    # GuardDuty triggers a finding around API calls made from Kali Linux, so let's avoid that...
	    self.print('Detected environment as one of Kali/Parrot/Pentoo Linux. Modifying user agent to hide that from GuardDuty...')