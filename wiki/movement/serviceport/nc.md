	>nc -znv 192.168.0.98 1-65535
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/321.png)

	>nc -v -w 1 192.168.0.110 -z 1-1000
	>for i in {101..102}; do nc -vv -n -w 1 192.168.0.$i 21-25 -z; done
