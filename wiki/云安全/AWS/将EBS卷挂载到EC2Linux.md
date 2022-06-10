	EBS 快照是块级增量，这意味着每个快照只复制卷中自上次快照以来已更改的块（或区域）。要恢复您的数据，您需要从您的一个 EBS 快照创建一个新的 EBS 卷。新卷将是创建快照的初始 EBS 卷的副本
	前往 EC2 –> Volumes 并创建一个您喜欢的大小和类型的新卷。
	选择创建的卷，右键单击并选择“附加卷”选项。
	从实例文本框中选择实例，如下所示：attach ebs volume
	aws ec2 create-volume –snapshot-id snapshot_id --availability-zone zone
	aws ec2 attach-volume –-volume-id volume_id –-instance-id instance_id --device device
	现在，登录到您的 ec2 实例并使用以下命令列出可用磁盘：lsblk
	使用以下命令检查卷是否有任何数据：sudo file -s /dev/xvdf
	使用以下命令将卷格式化为 ext4 文件系统：sudo mkfs -t ext4 /dev/xvdf
	创建一个您选择的目录来挂载我们的新 ext4 卷。我正在使用名称“newvolume”：sudo mkdir /newvolume
	使用以下命令将卷挂载到“newvolume”目录：sudo mount /dev/xvdf /newvolume/
	cd 进入 newvolume 目录并检查磁盘空间以确认卷安装：cd /newvolume; df -h .