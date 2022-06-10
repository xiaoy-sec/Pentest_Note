	>aws cloudtrail delete-trail --name cloudgoat_trail --profile administrator
	禁用对来自全局服务的事件的监控
	>aws cloudtrail update-trail --name cloudgoat_trail --no-include-global-service-event 
	在特定区域禁用 Cloud Trail
	>aws cloudtrail update-trail --name cloudgoat_trail --no-include-global-service-event --no-is-multi-region --region=eu-west