	获得 docker CLI、API 或 Socket时，对 docker 镜像进行后门操作，第一步将映像下载到本地“docker pull”，，第二部本地创建以目标镜像为基础的镜像，将后门留进去，“docker build”命令将此 Dockerfile 转换为映像，第三步传回去。
	参考
	● https://github.com/cr0hn/dockerscan
	● https://github.com/RhinoSecurityLabs/ccat