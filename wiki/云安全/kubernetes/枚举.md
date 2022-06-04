  #### Can I
	> kubectl auth can-i get secret
	执行“can-i”命令来查看我们是否可以访问机密。
  #### 基础设施
	Kubernetes 用于管理基础设施，作为攻击者，我们需要绘制出这个基础设施。 节点是运行在虚拟或物理服务器上的工作机器。
	> kubectl get nodes
	> kubectl get nodes -o yaml 来输出节点列表及其所有相关信息,返回的字段之一将是节点的外部 IP 地址
	绘制环境的 pod
	> kubectl get pods
  #### Secretes
	Secret 是一个包含少量敏感数据（例如密码、令牌或密钥）的对象。 使用 Secret 意味着开发人员无需在其应用程序代码中包含机密数据。 但是，默认情况下，秘码未加密地存储在 API 服务器的底层数据存储 (etcd) 中。 任何拥有 API 访问权限的人都可以检索或修改 Secret，任何拥有 etcd 访问权限的人都可以。 如果攻击者可以访问 kubernetes 机密，他们可能会泄露敏感信息。
	> kubectl get secret
  #### Configmap
	ConfigMap是一个 API 对象，用于在键值对中存储非机密数据。
	> kubectl describe configmap