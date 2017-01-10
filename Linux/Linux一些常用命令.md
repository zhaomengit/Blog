#### 1.查看Linux版本,类型

    lsb_release -v
    
#### 2.查看Linux内核版本

    uname -r
    
#### 3.查看cpu信息

    cat /proc/cpuinfo

    # 总核数 = 物理CPU个数 X 每颗物理CPU的核数 
    # 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

    # 查看物理CPU个数
    cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

    # 查看每个物理CPU中core的个数(即核数)
    cat /proc/cpuinfo| grep "cpu cores"| uniq

    # 查看逻辑CPU的个数
    cat /proc/cpuinfo| grep "processor"| wc -l
    
#### 4.查看Linux分区情况

    df -h
    
结果:    
    
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/sda4       2.0G  845M  989M  47% /
    devtmpfs         32G     0   32G   0% /dev
    tmpfs            32G     0   32G   0% /dev/shm
    tmpfs            32G   25M   32G   1% /run
    tmpfs            32G     0   32G   0% /sys/fs/cgroup
    /dev/sda2       9.8G  3.4G  5.9G  37% /usr
    /dev/sda5       3.9G   17M  3.6G   1% /tmp
    /dev/sda6       3.9G  2.2G  1.5G  61% /var
    /dev/sda7       3.2T  252M  3.1T   1% /data0
    tmpfs           6.3G     0  6.3G   0% /run/user/0
    
查看某个文件夹所属的分区

    df -h /opt
    
#### 5.查看当前目录各个文件夹大小

```
[zhaomengit@zm var]$ sudo du -h --max-depth=1 
144K    ./spool
4.0K    ./account
4.0K    ./preserve
24K     ./db
1.2G    ./cache
4.0K    ./opt
12K     ./kerberos
4.0K    ./games
4.0K    ./crash
136M    ./tmp
16K     ./lost+found
12K     ./target
33M     ./log
4.0K    ./adm
8.0K    ./var
4.0K    ./local
4.0K    ./gopher
36M     ./cfengine
2.5G    ./lib
8.0K    ./empty
4.0K    ./yp
4.0K    ./nis
3.8G    .
```
    
#### 6.查看打开某个端口的进程

查看打开8080端口的进程


```
lsof -i:8080
```

查看所有端口监听建立情况

```
netstat  -anplut
```

#### 7.使用curl上传文件

```
curl  -F "filename=@/home/test/file.tar.gz" http://localhost/action.php
```
curl陷阱注意:

```
curl -X GET  http://www.xxx.com/test?a=1&b=2
```

这个地方&是有问题的,Linux默认当做后台执行,这个地方可以这样改正

```
curl -X GET  'http://www.xxx.com/test?a=1&b=2' 或者 curl -X GET  'http://www.xxx.com/test?a=1\&b=2'
```
