	需要提取有关当前实例及其 AMI/安全组/子网的数据：aws ec2 describe-images --region eu-west-1
	为 instance-id 创建一个新图像
	>aws ec2 create-image --instance-id i-0438b003d81cd7ec5 --name "AWS Audit" --description "Export AMI" --region eu-west-1  

	将密钥添加到 AWS
	>aws ec2 import-key-pair --key-name "AWS Audit" --public-key-material file://~/.ssh/id_rsa.pub --region eu-west-1  

	使用之前创建的 AMI 创建 ec2，使用相同的安全组和子网轻松连接。
	>aws ec2 run-instances --image-id ami-0b77e2d906b00202d --security-group-ids "sg-6d0d7f01" --subnet-id subnet-9eb001ea --count 1 --instance-type t2.micro --key-name "AWS Audit" --query "Instances[0].InstanceId" --region eu-west-1

	现在你可以检查实例
	>aws ec2 describe-instances --instance-ids i-0546910a0c18725a1 

	如果需要：编辑组
	>aws ec2 modify-instance-attribute --instance-id "i-0546910a0c18725a1" --groups "sg-6d0d7f01"  --region eu-west-1

	清理我们的实例
	>aws ec2 stop-instances --instance-id "i-0546910a0c18725a1" --region eu-west-1 
	>aws ec2 terminate-instances --instance-id "i-0546910a0c18725a1" --region eu-west-1