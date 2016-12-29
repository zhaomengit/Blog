### 使用set命令

#### 不带选项执行set命令

不带选项执行set命令时，会输出当前shell的所有变量，输出格式就和shell脚本里面的变量赋值的格式一样：name=value。因此，set命令的输出可以直接作为一个stdin的输入。

#### 基本语法
set命令的基本语法如下（来自bash的man手册）：

```
    set [--abefhkmnptuvxBCEHPT] [-o option-name] [arg ...]
    set [+abefhkmnptuvxBCEHPT] [+o option-name] [arg ...]
```

set通过选项来开关shell的不同特性，每个特性都对应一个选项。每个特性都有两种配置方式：

一种是通过set -e和set +e这种形式，即直接指定选项。
另一种是通过set -o errexit和set +o errexit这种形式，即通过o这个选项来指定选项名。
我想你一定对选项是用+号还是-号十分好奇。在set命令中，选项前面跟着-号表示开启这个选项，+表示关闭这个选项。

#### 选项介绍

##### -o

执行set -o会输出当前的set选项配置情况：

```
~/programming/test$ set -o
allexport       off
braceexpand     on
emacs           on
errexit         off
errtrace        off
functrace       off
hashall         on
histexpand      on
history         on
ignoreeof       off
interactive-comments    on
keyword         off
monitor         on
noclobber       off
noexec          off
noglob          off
nolog           off
notify          off
nounset         off
onecmd          off
physical        off
pipefail        off
posix           off
privileged      off
verbose         off
vi              off
xtrace          off
+o
```

执行set +o也是输出当前的set选项的配置情况，只不过输出形式是一系列的set命令。这种输出形式一般用于重建当前的set配置项时使用。

```
~/programming/test$ set +o
set +o allexport
set -o braceexpand
set -o emacs
set +o errexit
set +o errtrace
set +o functrace
set -o hashall
set -o histexpand
set -o history
set +o ignoreeof
set -o interactive-comments
set +o keyword
set -o monitor
set +o noclobber
set +o noexec
set +o noglob
set +o nolog
set +o notify
set +o nounset
set +o onecmd
set +o physical
set +o pipefail
set +o posix
set +o privileged
set +o verbose
set +o vi
set +o xtrace
-e or -o errexit
```

设置了这个选项后，当一个命令执行失败时，shell会立即退出。

##### -n or -o noexec
设置了这个选项后，shell读取命令，但是不会执行它们。这个选项可以用来检查shell脚本是否存在语法错误。

##### -u or -o unset
设置了这个选项之后，当shell要扩展一个还未设置过值的变量时，shell必须输出信息到stderr，然后立即退出。但是交互式shell不应该退出。

##### -x or -o xtrace
设置了这个选项之后，对于每一条要执行的命令，shell在扩展了命令之后（参数扩展）、执行命令之前，输出trace到stderr。

##### -o pipefail
这个选项会影响管道的返回值。默认情况下，一个管道的返回值是最后一个命令的返回值，比如cmda | cmdb | cmdc这个管道，返回值是由cmdc命令的返回值决定的。如果指定了pipefail选项，那么管道的返回值就会由最后一个失败的命令决定，意思就是有命令失败就会返回非0值。如果所有命令都成功，则返回成功。

例子:

```
#!/bin/bash

set -o xtrace
set -o errexit  # 可以把这样注释掉看下执行效果有什么不一样。

echo "Before"
ls filenoexists  # ls也不存在的文件
echo "After"
```


##### -e

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

##### +e

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

##### 其他

|短符号| 长符号 | 结果|
| ------------- |:------------- | :----- |
|set -f| set -o noglob|禁止特殊字符用于文件名扩展。|
|set -v| set -o verbose|打印读入shell的输入行。|
|set -x| set -o xtrace	|执行命令之前打印命令。|


如果在脚本文件中加入了命令set –x ，那么在set命令之后执行的每一条命令以及加载命令行中的任何参数都会显示出来，
每一行都会加上加号（+），提示它是跟踪输出的标识，在子shell中执行的shell跟踪命令会加2个叫号（++）。

#### env 

在Linux的一些脚本里，需在开头一行指定脚本的解释程序，如： 
\#!/usr/bin/env Python 
再如： 
\#!/usr/bin/env perl 
\#!/usr/bin/env zimbu 

但有时候也用 
\#!/usr/bin/python 
和 
\#!/usr/bin/perl 

但有时候也用 
\#!/usr/bin/python 
和 
\#!/usr/bin/perl 

那么 env到底有什么用？何时用这个呢？ 
脚本用env启动的原因，是因为脚本解释器在linux中可能被安装于不同的目录，env可以在系统的PATH目录中查找。同时，env还规定一些系统环境变量。

而如果直接将解释器路径写死在脚本里，可能在某些系统就会存在找不到解释器的兼容性问题。有时候我们执行一些脚本时就碰到这种情况。
