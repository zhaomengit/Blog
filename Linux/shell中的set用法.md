
#### 1.set -e

```shell
#!/bin/bash
set -e  #"Exit immediately if a simple command exits with a non-zero status."
touch a.txt
echo "touch a.txt done"
i   #这里有个错误，马上终止脚本

for i in 1 2 3
do 
    echo "$i" >>a.txt
done
cat a.txt
echo "" > a.txt
echo "Hello World !" 
```

输出:
```
touch a.txt done
set.sh: line 5: i: command not found
```

### 2.set +e

```shell
#!/bin/bash
set +e  #"Exit immediately if a simple command exits with a non-zero status."
touch a.txt
echo "touch a.txt done"
i   #这里有个错误，报错但是继续执行下面的脚本

for i in 1 2 3
do 
    echo "$i" >>a.txt
done
cat a.txt
echo "" > a.txt
echo "Hello World !" 
```

输出:

```
touch a.txt done
set.sh: line 5: i: command not found
1
2
3
Hello World !
```

#### 3.其他

|短符号| 长符号 | 结果|
| ------------- |:------------- | :----- |
|set -f| set -o noglob|禁止特殊字符用于文件名扩展。|
|set -v| set -o verbose|打印读入shell的输入行。|
|set -x| set -o xtrace	|执行命令之前打印命令。|


如果在脚本文件中加入了命令set –x ，那么在set命令之后执行的每一条命令以及加载命令行中的任何参数都会显示出来，
每一行都会加上加号（+），提示它是跟踪输出的标识，在子shell中执行的shell跟踪命令会加2个叫号（++）。

#### 4.env 

在Linux的一些脚本里，需在开头一行指定脚本的解释程序，如： 
#!/usr/bin/env Python 
再如： 
#!/usr/bin/env perl 
#!/usr/bin/env zimbu 

但有时候也用 
#!/usr/bin/python 
和 
#!/usr/bin/perl 

但有时候也用 
#!/usr/bin/python 
和 
#!/usr/bin/perl 

那么 env到底有什么用？何时用这个呢？ 
脚本用env启动的原因，是因为脚本解释器在linux中可能被安装于不同的目录，env可以在系统的PATH目录中查找。同时，env还规定一些系统环境变量。

而如果直接将解释器路径写死在脚本里，可能在某些系统就会存在找不到解释器的兼容性问题。有时候我们执行一些脚本时就碰到这种情况。
