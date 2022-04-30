	https://github.com/mhaskar/Shellcode-In-Memory-Decoder
	流程
	打开一个进程并检索该进程的HANDLE。
	在进程中分配空间（检索内存地址）。
	将数据（shellcode）写入该进程中。
	执行shellcode。
	我们可以使用几个Win32 API执行这些步骤：
	OpenProcess()
	VirtualAllocEx()
	WriteProcessMemory()
	CreateRemoteThread()
	正常情况下，我们将原始shellcode直接写入到内存中，但是如果AV /EDR检测到了Shellcode，它们肯定会发出警报
	所以我们在二进制文件中使用可逆的方式把shellcode编码，再解码写入内存来规避防护。
	比如加、减、异或、交换。
	使用cs生成个shellcode
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/759.png)
	
	使用python进行异或
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/760.png)

	该脚本读取我们的shellcode的每个操作码，然后将其与字节0x01（在这种情况下为我们的密钥）进行异或，将其打印为如下的shellcode ：
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/761.png)

	现在，我们将开始实现将为我们执行shellcode注入的C代码。
	编译方式
	x86_64-w64-mingw32-gcc decoder.c -o decoder.exe -w
	我将逐步介绍每个win32 API。
	打开过程并获取一个句柄
	我们需要选择一个向其注入shellcode的进程，然后需要检索该过程的句柄，以便可以对其执行一些操作，我们将使用OpenProcess win32 API
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/762.png)

	该代码将您要获取其句柄的进程ID作为第一个参数，然后它将使用具有PROCESS_ALL_ACCESS访问权限的OpenProcess()来打开该进程并将该句柄保存在变量process中，最后，为我们打印
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/763.png)

	成功检索到该句柄
	检索句柄后的下一步将是在该进程内分配空间，我们可以使用VirtualAllocEx()
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/764.png)

	base_address代表分配的内存的地址
	16行，我们将打印分配的内存的地址，并将其写入数据
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/765.png)

	0x29d0000作为地址，
	使用x64dbg附加explorer.exe进程，转到这里看看
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/766.png)

	可以看到函数VirtualAllocEx已为我们在explorer.exe中分配了内存空间，我们准备写入数据。
	接下来我们解码shellcode并写到内存中
	即使使用这种类型（这里用的是异或）的编码，我们的shellcode也可能会被标记，因此请确保在操作中使用之前使用更强的编码并对其进行测试。
	这里为测试就只使用的0x01
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/767.png)

	这段代码将使用密钥0x01对每个字节进行解码后，将我们的shellcode写入内存中
	运行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/768.png)

	如图所见，我们将每个字节写入地址，现在我们用x64dbg进行调试，然后转到地址 0x3ce0000查看一下：
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/769.png)

	可以看到shellcode已经写入进去了。
	下一步就是执行shellcode了
	使用CreateRemoteThread()函数来执行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/770.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/771.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/772.png)
