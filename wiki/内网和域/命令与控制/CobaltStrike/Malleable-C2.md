	Cobalt Strike - Malleable C2 配置文件https://github.com/xx0hcd/Malleable-C2-Profiles
	Cobalt Strike Malleable C2 设计和参考指南https://github.com/threatexpress/malleable-c2
	Malleable-C2-Profiles https://github.com/rsmudge/Malleable-C2-Profiles
	SourcePoint 是一个 C2 配置文件生成器https://github.com/Tylous/SourcePoint

	语法示例
	set useragent "SOME AGENT"; # GOOD
	set useragent 'SOME AGENT'; # BAD
	prepend "This is an example;";

	双引号
	append "here is \"some\" stuff";
	反斜杠
	append "more \\ stuff";
	特殊字符
	prepend "!@#$%^&*()";

	执行命令./c2lint 来判断时候有错误
	如果 c2lint 完成且没有错误，则返回结果 0
	如果 c2lint 仅以警告完成，则返回结果 1
	如果 c2lint 仅以错误完成，则返回结果 2
	如果 c2lint 完成并出现错误和警告，则返回结果 3