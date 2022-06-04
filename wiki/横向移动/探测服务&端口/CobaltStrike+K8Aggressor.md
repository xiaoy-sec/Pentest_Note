	https://github.com/k8gege/Aggressor
  #### 存活主机
	beacon>Cscan 192.168.0.0/24 OnlinePC
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/323.png)
  #### MS17010
	beacon>Cscan 192.168.0.0/24 MS17010
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/324.png)
  #### 操作系统信息
	beacon>Cscan 192.168.0.0/24 Osscan
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/325.png)
  #### 内网站点banner、标题扫描
	beacon>Cscan 192.168.0.0/24 WebScan
  #### FTP爆破
	上传账户密码文件user.txt、pass.txt到beacon目录(beacon>pwd)
	beacon>Cscan 192.168.0.0/24 FtpScan
  #### WMI爆破windows账户密码
	上传账户密码文件user.txt、pass.txt到beacon目录(beacon>pwd)
	beacon>Cscan 192.168.0.0/24 WmiScan
  #### 思科设备扫描
	beacon>Cscan 192.168.0.0/24 CiscoScan
  #### 枚举共享
	beacon> EnumShare
  #### 枚举SQL SERVER数据库
	beacon> EnumMSSQL
