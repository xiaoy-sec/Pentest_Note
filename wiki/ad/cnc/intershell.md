	>python2 -c 'import pty;pty.spawn("/bin/sh")'
	>python3 -c "import pty;pty.spawn('/bin/bash')"
	>expect -c 'spawn bash;interact'