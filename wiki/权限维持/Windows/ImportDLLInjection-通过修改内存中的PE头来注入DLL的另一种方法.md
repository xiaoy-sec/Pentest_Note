	将修改新创建进程内存中的 PE 标头，以将我们自己的 DLL 添加到导入描述符列表中
	1 使用带有 CREATE_SUSPENDED 标志的 CreateProcess 创建目标 EXE 进程的挂起实例。
	2 计算目标EXE的基地址，使用NtQueryInformationProcess读取目标进程的PEB地址，使用ReadProcessMemory读取ImageBaseAddress字段值。
	3 使用 ReadProcessMemory 从目标进程读取主 PE 头（IMAGE_NT_HEADERS）。
	4 为以后存储未修改的 IMAGE_NT_HEADERS 结构的副本。
	5 在 PE 头 (ImageNtHeader.OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT]) 内的导入描述符列表中添加一个额外的 DLL 条目。还需要在目标进程中分配模块路径、导入查找表和导入地址表。导入表将包括一个条目：序数 #1。
	6 使用WriteProcessMemory 将更新的PE 头写回目标进程。
	7 使用 ResumeThread 恢复目标进程的执行。
	8 循环使用ReadProcessMemory 读取注入DLL 的新导入地址表。等到目标进程更新了单个导入（序数 #1）的地址。
	9 DLL 现在已经被注入到目标进程中。释放任何临时内存并从第 4 步恢复原始 PE 标头。
```c++
#include <stdio.h>
#include <windows.h>

DWORD (WINAPI *NtQueryInformationProcess)(HANDLE ProcessHandle, DWORD ProcessInformationClass, PVOID ProcessInformation, DWORD ProcessInformationLength, DWORD *ReturnLength);

struct PROCESS_BASIC_INFORMATION
{
	DWORD ExitStatus;
	void *PebBaseAddress;
	DWORD AffinityMask;
	DWORD BasePriority;
	DWORD UniqueProcessId;
	DWORD InheritedFromUniqueProcessId;
};

DWORD LaunchTargetProcess(char *pExePath, HANDLE *phProcess, HANDLE *phProcessMainThread)
{
	PROCESS_INFORMATION ProcessInfo;
	STARTUPINFO StartupInfo;

	// initialise startup data
	memset((void*)&StartupInfo, 0, sizeof(StartupInfo));
	StartupInfo.cb = sizeof(StartupInfo);

	printf("Launching target process...\n");

	// create target process (suspended)
	memset((void*)&ProcessInfo, 0, sizeof(ProcessInfo));
	if(CreateProcess(NULL, pExePath, NULL, NULL, 0, CREATE_SUSPENDED, NULL, NULL, &StartupInfo, &ProcessInfo) == 0)
	{
		return 1;
	}

	// store handles
	*phProcess = ProcessInfo.hProcess;
	*phProcessMainThread = ProcessInfo.hThread;

	return 0;
}

DWORD InjectDll(HANDLE hProcess, HANDLE hProcessMainThread, char *pDllPath)
{
	IMAGE_DOS_HEADER ImageDosHeader;
	IMAGE_NT_HEADERS ImageNtHeader;
	IMAGE_NT_HEADERS ImageNtHeader_Original;
	PROCESS_BASIC_INFORMATION ProcessBasicInfo;
	DWORD dwExeBaseAddrPebPtr = 0;
	DWORD dwExeBaseAddr = 0;
	DWORD dwNtHeaderAddr = 0;
	DWORD dwDllPathLength = 0;
	void *pRemoteAlloc_DllPath = NULL;
	void *pRemoteAlloc_ImportLookupTable = NULL;
	void *pRemoteAlloc_ImportAddressTable = NULL;
	void *pRemoteAlloc_NewImportDescriptorList = NULL;
	DWORD dwImportLookupTable[2];
	DWORD dwExistingImportDescriptorEntryCount = 0;
	DWORD dwNewImportDescriptorEntryCount = 0;
	BYTE *pNewImportDescriptorList = NULL;
	IMAGE_IMPORT_DESCRIPTOR NewDllImportDescriptors[2];
	DWORD dwExistingImportDescriptorAddr = 0;
	BYTE *pCopyImportDescriptorDataPtr = NULL;
	DWORD dwNewImportDescriptorListDataLength = 0;
	DWORD dwOriginalProtection = 0;
	DWORD dwOriginalProtection2 = 0;
	DWORD dwCurrentImportAddressTable[2];

	printf("Reading image base address from PEB...\n");

	// get process info
	memset(&ProcessBasicInfo, 0, sizeof(ProcessBasicInfo));
	if(NtQueryInformationProcess(hProcess, 0, &ProcessBasicInfo, sizeof(ProcessBasicInfo), NULL) != 0)
	{
		return 1;
	}

	// get target exe base address from PEB
	dwExeBaseAddrPebPtr = (DWORD)ProcessBasicInfo.PebBaseAddress + 8;
	if(ReadProcessMemory(hProcess, (void*)dwExeBaseAddrPebPtr, (void*)&dwExeBaseAddr, sizeof(DWORD), NULL) == 0)
	{
		return 1;
	}

	printf("Reading PE headers...\n");

	// read PE header from target process
	memset((void*)&ImageDosHeader, 0, sizeof(ImageDosHeader));
	if(ReadProcessMemory(hProcess, (void*)dwExeBaseAddr, (void*)&ImageDosHeader, sizeof(ImageDosHeader), NULL) == 0)
	{
		return 1;
	}

	// read NT header from target process
	dwNtHeaderAddr = dwExeBaseAddr + ImageDosHeader.e_lfanew;
	memset((void*)&ImageNtHeader, 0, sizeof(ImageNtHeader));
	if(ReadProcessMemory(hProcess, (void*)dwNtHeaderAddr, (void*)&ImageNtHeader, sizeof(ImageNtHeader), NULL) == 0)
	{
		return 1;
	}

	// save a copy of the original NT header
	memcpy((void*)&ImageNtHeader_Original, (void*)&ImageNtHeader, sizeof(ImageNtHeader_Original));

	// calculate dll path length
	dwDllPathLength = strlen(pDllPath) + 1;

	// allocate buffer for the dll path in the remote process
	pRemoteAlloc_DllPath = VirtualAllocEx(hProcess, NULL, dwDllPathLength, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
	if(pRemoteAlloc_DllPath == NULL)
	{
		return 1;
	}

	// write dll path to remote process buffer
	if(WriteProcessMemory(hProcess, (void*)pRemoteAlloc_DllPath, pDllPath, dwDllPathLength, NULL) == 0)
	{
		return 1;
	}

	// set import lookup table values (import ordinal #1)
	dwImportLookupTable[0] = 0x80000001;
	dwImportLookupTable[1] = 0;

	// allocate buffer for the new import lookup table in the remote process
	pRemoteAlloc_ImportLookupTable = VirtualAllocEx(hProcess, NULL, sizeof(dwImportLookupTable), MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
	if(pRemoteAlloc_ImportLookupTable == NULL)
	{
		return 1;
	}

	// write import lookup table to remote process buffer
	if(WriteProcessMemory(hProcess, (void*)pRemoteAlloc_ImportLookupTable, (void*)dwImportLookupTable, sizeof(dwImportLookupTable), NULL) == 0)
	{
		return 1;
	}

	// allocate buffer for the new import address table in the remote process
	pRemoteAlloc_ImportAddressTable = VirtualAllocEx(hProcess, NULL, sizeof(dwImportLookupTable), MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
	if(pRemoteAlloc_ImportAddressTable == NULL)
	{
		return 1;
	}

	// write import address table to remote process buffer (initially the same values as the import lookup table)
	if(WriteProcessMemory(hProcess, (void*)pRemoteAlloc_ImportAddressTable, (void*)dwImportLookupTable, sizeof(dwImportLookupTable), NULL) == 0)
	{
		return 1;
	}

	// set import descriptor values for injected dll
	NewDllImportDescriptors[0].OriginalFirstThunk = (DWORD)pRemoteAlloc_ImportLookupTable - dwExeBaseAddr;
	NewDllImportDescriptors[0].TimeDateStamp = 0;
	NewDllImportDescriptors[0].ForwarderChain = 0;
	NewDllImportDescriptors[0].Name = (DWORD)pRemoteAlloc_DllPath - dwExeBaseAddr;
	NewDllImportDescriptors[0].FirstThunk = (DWORD)pRemoteAlloc_ImportAddressTable - dwExeBaseAddr;

	// end of import descriptor chain
	NewDllImportDescriptors[1].OriginalFirstThunk = 0;
	NewDllImportDescriptors[1].TimeDateStamp = 0;
	NewDllImportDescriptors[1].ForwarderChain = 0;
	NewDllImportDescriptors[1].Name = 0;
	NewDllImportDescriptors[1].FirstThunk = 0;

	// calculate existing number of imported dll modules
	dwExistingImportDescriptorEntryCount = ImageNtHeader.OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT].Size / sizeof(IMAGE_IMPORT_DESCRIPTOR);

	if(dwExistingImportDescriptorEntryCount == 0)
	{
		// the target process doesn't have any imported dll entries - this is highly unusual but not impossible
		dwNewImportDescriptorEntryCount = 2;
	}
	else
	{
		// add one extra dll entry
		dwNewImportDescriptorEntryCount = dwExistingImportDescriptorEntryCount + 1;
	}

	// allocate new import description list (local)
	dwNewImportDescriptorListDataLength = dwNewImportDescriptorEntryCount * sizeof(IMAGE_IMPORT_DESCRIPTOR);
	pNewImportDescriptorList = (BYTE*)malloc(dwNewImportDescriptorListDataLength);
	if(pNewImportDescriptorList == NULL)
	{
		return 1;
	}

	if(dwExistingImportDescriptorEntryCount != 0)
	{
		// read existing import descriptor entries
		dwExistingImportDescriptorAddr = dwExeBaseAddr + ImageNtHeader.OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT].VirtualAddress;
		if(ReadProcessMemory(hProcess, (void*)dwExistingImportDescriptorAddr, pNewImportDescriptorList, dwExistingImportDescriptorEntryCount * sizeof(IMAGE_IMPORT_DESCRIPTOR), NULL) == 0)
		{
			free(pNewImportDescriptorList);
			return 1;
		}
	}

	// copy the new dll import (and terminator entry) to the end of the list
	pCopyImportDescriptorDataPtr = pNewImportDescriptorList + dwNewImportDescriptorListDataLength - sizeof(NewDllImportDescriptors);
	memcpy(pCopyImportDescriptorDataPtr, (void*)NewDllImportDescriptors, sizeof(NewDllImportDescriptors));

	// allocate buffer for the new import descriptor list in the remote process
	pRemoteAlloc_NewImportDescriptorList = VirtualAllocEx(hProcess, NULL, dwNewImportDescriptorListDataLength, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
	if(pRemoteAlloc_NewImportDescriptorList == NULL)
	{
		free(pNewImportDescriptorList);
		return 1;
	}

	// write new import descriptor list to remote process buffer
	if(WriteProcessMemory(hProcess, (void*)pRemoteAlloc_NewImportDescriptorList, pNewImportDescriptorList, dwNewImportDescriptorListDataLength, NULL) == 0)
	{
		free(pNewImportDescriptorList);
		return 1;
	}

	// free local import descriptor list buffer
	free(pNewImportDescriptorList);

	printf("Updating PE headers...\n");

	// change the import descriptor address in the remote NT header to point to the new list
	ImageNtHeader.OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT].VirtualAddress = (DWORD)pRemoteAlloc_NewImportDescriptorList - dwExeBaseAddr;
	ImageNtHeader.OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT].Size = dwNewImportDescriptorListDataLength;

	// make NT header writable
	if(VirtualProtectEx(hProcess, (LPVOID)dwNtHeaderAddr, sizeof(ImageNtHeader), PAGE_EXECUTE_READWRITE, &dwOriginalProtection) == 0)
	{
		return 1;
	}

	// write updated NT header to remote process
	if(WriteProcessMemory(hProcess, (void*)dwNtHeaderAddr, (void*)&ImageNtHeader, sizeof(ImageNtHeader), NULL) == 0)
	{
		return 1;
	}

	printf("Resuming process...\n");

	// resume target process execution
	ResumeThread(hProcessMainThread);

	printf("Waiting for target DLL...\n");

	// wait for the target process to load the DLL
	for(;;)
	{
		// read the IAT table for the injected DLL
		memset((void*)dwCurrentImportAddressTable, 0, sizeof(dwCurrentImportAddressTable));
		if(ReadProcessMemory(hProcess, (void*)pRemoteAlloc_ImportAddressTable, (void*)dwCurrentImportAddressTable, sizeof(dwCurrentImportAddressTable), NULL) == 0)
		{
			return 1;
		}

		// check if the IAT table has been processed
		if(dwCurrentImportAddressTable[0] == dwImportLookupTable[0])
		{
			// IAT table for injected DLL not yet processed - try again in 100ms
			Sleep(100);

			continue;
		}

		// DLL has been loaded by target process
		break;
	}

	printf("Restoring original PE headers...\n");

	// restore original NT headers in target process
	if(WriteProcessMemory(hProcess, (void*)dwNtHeaderAddr, (void*)&ImageNtHeader_Original, sizeof(ImageNtHeader), NULL) == 0)
	{
		return 1;
	}

	// restore original protection value for remote NT headers
	if(VirtualProtectEx(hProcess, (LPVOID)dwNtHeaderAddr, sizeof(ImageNtHeader), dwOriginalProtection, &dwOriginalProtection2) == 0)
	{
		return 1;
	}

	// free temporary memory in remote process
	VirtualFreeEx(hProcess, pRemoteAlloc_DllPath, 0, MEM_RELEASE);
	VirtualFreeEx(hProcess, pRemoteAlloc_ImportLookupTable, 0, MEM_RELEASE);
	VirtualFreeEx(hProcess, pRemoteAlloc_ImportAddressTable, 0, MEM_RELEASE);
	VirtualFreeEx(hProcess, pRemoteAlloc_NewImportDescriptorList, 0, MEM_RELEASE);

	return 0;
}

int main(int argc, char *argv[])
{
	HANDLE hProcess = NULL;
	HANDLE hProcessMainThread = NULL;
	char *pExePath = NULL;
	char *pInjectDllPath = NULL;
	char szInjectDllFullPath[512];

	printf("ImportDLLInjection - www.x86matthew.com\n\n");

	if(argc != 3)
	{
		printf("Usage: %s [exe_path] [inject_dll_path]\n\n", argv[0]);

		return 1;
	}

	// get params
	pExePath = argv[1];
	pInjectDllPath = argv[2];

	// get full path from dll filename
	memset(szInjectDllFullPath, 0, sizeof(szInjectDllFullPath));
	if(GetFullPathName(pInjectDllPath, sizeof(szInjectDllFullPath) - 1, szInjectDllFullPath, NULL) == 0)
	{
		printf("Invalid DLL path\n");

		return 1;
	}

	// get NtQueryInformationProcess function ptr
	NtQueryInformationProcess = (unsigned long (__stdcall *)(void *,unsigned long,void *,unsigned long,unsigned long *))GetProcAddress(GetModuleHandle("ntdll.dll"), "NtQueryInformationProcess");
	if(NtQueryInformationProcess == NULL)
	{
		printf("Failed to find NtQueryInformationProcess function\n");

		return 1;
	}

	// launch target process
	if(LaunchTargetProcess(pExePath, &hProcess, &hProcessMainThread) != 0)
	{
		printf("Failed to launch target process\n");

		return 1;
	}

	// inject DLL into target process
	if(InjectDll(hProcess, hProcessMainThread, szInjectDllFullPath) != 0)
	{
		printf("Failed to inject DLL\n");

		// error
		TerminateProcess(hProcess, 0);
		CloseHandle(hProcessMainThread);
		CloseHandle(hProcess);

		return 1;
	}

	printf("Finished\n");

	// close handles
	CloseHandle(hProcessMainThread);
	CloseHandle(hProcess);

	return 0;
}
```
	演示dll
```c++
#include <stdio.h>
#include <windows.h>

// a blank exported function - this will be ordinal #1
__declspec(dllexport) void __cdecl ExportedFunction(void)
{
}

BOOL WINAPI DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpReserved)
{
	if(fdwReason == DLL_PROCESS_ATTACH)
	{
		// display a message box
		MessageBox(NULL, "MessageBox from injected DLL", "www.x86matthew.com", MB_OK);
	}

    return 1;
}
```
