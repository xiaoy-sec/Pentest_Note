	无需管理员权限
	下载免安装portable版本http://rustdesk.com/zh/ https://github.com/rustdesk/rustdesk/releases
	上传文件后运行，再结束进程，修改配置文件的密码字段，再运行
	配置文件在：C:\Users\用户名\AppData\Roaming\RustDesk\config\RustDesk.toml
	访问不出网机器修改配置文件RustDesk2.toml，在options下添加一行
	direct-server = 'Y' 
	direct-access-port = '8443'后重启RustDesk（依据防火墙规则选择端口）
	IP:PORT格式方式链接