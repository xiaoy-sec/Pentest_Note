	https://gist.github.com/hvelarde/ceac345c662429447959625e6feb2b47
	通过状态码获取请求总数
	awk '{print $9}' /var/log/apache2/access.log | sort | uniq -c | sort –rn
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/716.png)

	按照IP的请求数量排序
	awk '{print $1}' /var/log/apache2/access.log | sort | uniq -c | sort -rn | head | awk -v OFS='\t' '{"host " $2 | getline ip; print $0, ip}'
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/717.png)

	按照ua的请求数量排序
	awk -F'"' '{print $6}' /var/log/apache2/access.log | sort | uniq -c | sort -rn | head
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/718.png)

	按照url的请求数量排序
	awk '{print $7}' /var/log/apache2/access.log | sort | uniq -c | sort -rn | head
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/719.png)
	
	按照请求页面为404的url排序
	awk '$9 ~ /404/ {print $7}' /var/log/apache2/access.log | sort | uniq -c | sort -rn | head
	按照请求致后端报错的IP排序
	awk '$0 ~ /\[error\]/ && match($0, /(client: )(.*)(, server)/, arr) {print arr[2]}' /var/log/apache2/error.log | sort | uniq -c | sort -rn | awk -v OFS='\t' '{"host " $2 | getline ip; print $0, ip}'
	获取最近10分钟的请求
	awk -v date=$(date +[%d/%b/%Y:%H:%M --date="-10 minutes") '$4 > date' /var/log/nginx/access.log
