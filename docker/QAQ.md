
刚安装完docker出现以下问题：

```
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

将下面一行加入到/etc/default/docker以激活Docker在TCP的API：

```
DOCKER_OPTS="-H 127.0.0.1:4243"
```

然后重启Docker:

```
$ sudo service docker restart
```

```

WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disabled
```

`vim  /etc/sysctl.conf`

```
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-arptables = 1
```

```
sysctl -p
```

```
vim /etc/systemd/system/docker.service.d
```
