  #### LD_PRELOAD
	linux环境
	putenv()、mail()可用
	https://github.com/yangyangwithgnu/bypass_disablefunc_via_LD_PRELOAD
	http://192.168.0.107/bypass_disablefunc.php?cmd=pwd&outpath=/tmp/xx&sopath=/var/www/bypass_disablefunc_x64.so
	outpath是命令输出位置，sopath指定so文件路径。
	或
	替换php文件中的mail为error_log("a",1);
  #### php7.0-7.3 bypass
	直接bypass
	https://raw.githubusercontent.com/mm0r1/exploits/master/php7-gc-bypass/exploit.php