	当您访问云帐户时，您需要确定的第一件事就是您也可以访问哪些服务。根据您的权限，这可能是直截了当或稍微困难一些。如果您拥有所有者、编辑者或查看者等任何原始角色，您可能拥有在云环境中发现所有内容所需的所有权限，只需使用 Gcloud、Gsutil、Google SDK 库或 Google API 并开始查询即可。
  #### 工具
	在这个阶段可以使用几个工具，如下所示：
	● Gcloud
	● Gsutil
	● 适用于您的编程语言的 Google SDK 库
	● 谷歌API
  #### 桶
	> Gsutil ls
	如果您只有访问帐户，您可以使用以下命令直接查询 google api：
	> curl -X GET -H "X-Goog-User-Project: PROJECT-HERE" -H "Content-Type:application/json" -H "Authorization: Bearer ACCESS-TOKEN-HERE" https://storage.googleapis.com/storage/v1/b?project=PROJECT-HERE
	获得存储桶列表后，您就可以开始查看其中的内容。人们在这里存储各种东西，比如凭据、加密密钥以及其他任何被认为是敏感的东西。 在发现阶段，您应该已经发现了目标 GCP 环境中的所有存储桶，现在是时候环顾四周，看看您能找到什么
	> gsutil ls gs://STORAGE_BUCKET_NAME
	使用“cat”命令转储文件的内容：
	> gsutil cat gs://SOTRAGE_BUCKET_FILE
	传送到一个文件并在本地打开它。 您还可以将整个存储桶复制到本地计算机：
	> gsutil cp -r dir gs://STORAGE_BUCKET_NAME
  #### 实例
	您的目标云环境可能正在运行多个虚拟机。 绘制您的环境以查看哪些机器正在运行以及您可以访问哪些机器是一个好主意。 这可以使用 gcloud 完成，如下所示：
	> gcloud compute instances list
	如果您只有访问令牌，您还可以使用计算 google api 来提取此信息
  #### 数据库
	使用 gcloud 和 google API 来查找这些实例
	> gcloud sql instances list
	> gcloud spanner instances list
	> gcloud bigtable instances list
  #### 加密和解密密钥
	内容存储在 GCP（存储桶）上时，有的将它们存储为纯文本，有的使用密钥对其进行加密。钥存储在密钥管理系统 (KMS) 中，如果有权访问密钥，则可以使用该密钥来解密敏感文件。 使用以下命令查找加密密钥：
	> gcloud kms keyrings list --location global
	> gcloud kms keys --keyring KEY-RING-NAME --location global
  #### 图片
	下载它们，看看它们上有什么秘密
	> gcloud compute images list --no-standard-images
  #### 容器
	可参考docker hacking
	> gcloud container images list
  #### Kubernetes
	与其他云平台不同，kubernetes 在 GCP 用户中非常受欢迎。
	> gcloud container clusters list
  #### NotSoSecure 云服务枚举工具
	● https://github.com/NotSoSecure/cloud-service-enum  这个工具的好处是它需要一个访问密钥而不是服务帐户密钥。
	> python3 gcp_service_enum.py --access-token ACCESS-TOKEN-HERE --project-id PROJECT-ID-HERE