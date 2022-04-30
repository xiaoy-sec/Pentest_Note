	>find / -name index.php
	查找木马文件
	>find . -name '*.php' | xargs grep -n 'eval('
	>find . -name '*.php' | xargs grep -n 'assert('
	>find . -name '*.php' | xargs grep -n 'system('