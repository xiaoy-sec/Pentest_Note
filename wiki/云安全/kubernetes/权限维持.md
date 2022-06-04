  #### 定时任务
    Cron 可用于调度在指定 pod 上运行的命令。 由于我们可以在 pod 上运行 bash 命令，因此攻击者可以读取服务帐户令牌并将其发送给攻击者。 攻击者可以使用此令牌对 Kubernetes 进行身份验证。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/904.png)

    创建 yaml 文件后，使用以下命令创建 cron 作业：
    > kubectl create -f cron-job.yaml
    如上所示，cron 作业每天凌晨 1:00 发出一个 curl 命令，它将“/var/run/secrets/kubernetes.io/serviceaccount/token”的内容发送到我们的域。 一旦我们将服务器帐户令牌附加到 pod，我们就可以登录。