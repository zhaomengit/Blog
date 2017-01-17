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

#### 8.回到上次操作的目录

```
cd -  # 进入上次访问目录
```

#### 9.历史命令搜索操作快捷键：

```
[Ctrl + r], [Ctrl + p], [Ctrl + n]
```

在终端中按捉 [Ctrl] 键的同时 [r] 键，出现提示：(reverse-i-search), 
此时你尝试一下输入你以前输入过的命令，当你每输入一个字符的时候，终端都会滚动显示你的历史命令。
当显示到你想找的合适的历史命令的时候，直接 [Enter]，就执行了历史命令。

另外， [Ctrl + p] 或 [Ctrl + n] 快速向前或向后滚动查找一个历史命令，
对于快速提取刚刚执行过不久的命令很有用。

#### 10.命令行内快速操作键

1. 移动操作快捷键

        Ctrl + f-- 向右移动一个字符，当然多数人用→
        Ctrl + b-- 向左移动一个字符， 多数人用←
        ESC + f-- 向右移动一个单词，MAC下建议用ALT + →
        ESC + b-- 向左移动一个单词，MAC下建议用ALT + ←
        Ctrl + a-- 跳到行首
        Ctrl + e-- 跳到行尾

2. 删除操作快捷键

        Ctrl + d-- 向右删除一个字符
        Ctrl + h-- 向左删除一个字符
        Ctrl + u-- 删除当前位置字符至行首（输入密码错误的时候多用下这个）
        Ctrl + k-- 删除当前位置字符至行尾
        Ctrl + w-- 删除从光标到当前单词开头

3. 其他操作快捷键

        Ctrl + y-- 插入最近删除的单词
        Ctrl + c-- 终止操作
        Ctrl + d-- 当前操作转到后台
        Ctrl + l-- 清屏 （有时候为了好看）
