### 问题描述

在`centos 7`中, `docker pull`私有仓库的镜像,
私有仓库是http协议的,出现以下错误

```
Error response from daemon: Get https://registry.intra.weibo.com/v1/_ping: dial tcp 172.16.234.194:443: getsockopt: connection refused
```
### 解决方法
修改配置文件,需要在`/usr/lib/systemd/system/docker.service`这个文件中更改

```
ExecStart=/usr/bin/docker daemon -H fd:// \
         --insecure-registry registry.intra.weibo.com --insecure-registry 10.77.110.67 --insecure-registry 10.75.5.125
```
增加了以下内容:

```
         --insecure-registry registry.intra.weibo.com --insecure-registry 10.77.110.67 --insecure-registry 10.75.5.125
```

然后执行:

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```
