避免定时任务脚本的常见问题
很多脚本在实际使用的时候往往是以定时任务的方式运行，而非手工运行。但是实现同样功能的脚本在这两种运行方式下可能遇到的问题不尽相同。
以定时任务方式运行的脚本往往会遇到以下几个问题。
路径问题：当前目录往往不是脚本文件所在目录。因此，脚本在引用其使用的外部文件，如配置文件和其它脚本文件时，无法方便得使用相对路径。
命令找不到问题：脚本中使用到的一些外部命令，在手工执行脚本的时候可以正常调用。但是在定时任务下运行则可能出现脚本解析器找不到相关命令的问题。
脚本重复运行问题：一次脚本的执行未结束，而下一次脚本的运行已经开始。导致系统中有多个进程在同时运行同一个脚本。
下面分享定时任务脚本开发中上述几个常见问题的处理方法。
路径问题
定时任务下当前路径往往不是脚本文件所在目录。因此我们需要用绝对路径来引用。即先获取脚本所在目录，然后以该目录为基础采用绝对路径的方式去引用脚本所需的外部文件。方法如下面代码所示。
清单 1. 获取脚本文件所在路径
#!/usr/bin/ksh

echo "Current path is: `pwd`"
scriptPath=`dirname $0` #获取脚本所在路径

echo "The script is located at: $scriptPath"
cat "$scriptPath/readme" #使用绝对路径引用外部文件
将清单 1 中的脚本置于目录/opt/demo/scripts/auto-task 下，并在 cron 中添加该脚本。定时任务运行输出如下。
Current path is: /home/viscent
The script is located at: /opt/demo/scripts/auto-task
命令找不到问题
定时任务下运行的脚本可能出现脚本解析器找不到相关命令的问题。比如 Oracle 数据库中的 sqlplus 命令，脚本在调用该命令时若没有特殊处理则在定时任务下执行会使脚本解析器无法找到这个命令，出现如下所示的错误提示：
sqlplus: command not found
这是因为脚本在定时任务下执行时脚本是由非登录式 Shell 来执行的，并且执行脚本的父 Shell 并非 Oracle 用户的 Shell。因此，此时 Oracle 用户的.profile 文件并没有被调用。故解决的方法是在脚本的开头添加以下代码：
清单 2. 解决找不到外部命令问题
source /home/oracle/.profile
也就说，对于外部命令找不到的问题，可以通过在脚本的开头加一个 source 用户的.profile 文件的语句来解决。
脚本重复运行问题
定时任务脚本的另外一个常见问题是脚本重复运行的问题。比如，一个脚本被设置为每 5 分钟运行一次。若某一次该脚本的运行无法在 5 分钟内结束的话，定时任务服务仍然会新启一个进程来执行该脚本。这时就出现了运行同一个脚本的多个进程。而这可能导致脚本功能紊乱。并且浪费了系统资源。 避免脚本重复运行的方法通常有两种。一是在脚本执行时先检查系统是否存在运行该脚本的其它进程。若存在，则终止当前脚本的运行。二是，脚本运行时检查系统中是否存在其它进程运行该脚本。若存在，则结束那个进程（此方法有一定风险，慎用！）。这两种方法均需要在脚本的开头检查系统是否已经存在运行当前脚本的进程，若存在这样的进程则获取该进程的 PID。示例代码如下清单 3 所示。
清单 3. 防止脚本重复运行方法 1
#!/usr/bin/ksh

main(){
selfPID="$$"
scriptFile="$0"

typeset existingPid
existingPid=`getExistingPIDs $selfPID "$scriptFile"`

if [ ! -z "$existingPid" ]; then
  echo "The script already running, exiting..."
  exit -1
fi

doItsTask

}

#获取除本身进程以外其它运行当前脚本的进程的 PID
getExistingPIDs(){
selfPID="$1"
scriptFile="$2"

ps -ef | grep "/usr/bin/ksh ${scriptFile}" | grep -v "grep" | awk "{ if(\$2!=$selfPID) print \$2 }"
}

doItsTask(){
echo "Task is now being executed..."
sleep 20  #睡眠 20s，以模拟脚本在执行需要长时间完成的任务
}

main $*
清单 4. 防止脚本重复运行方法 2
#!/usr/bin/ksh

main(){
selfPID="$$"
scriptFile="$0"

typeset existingPid
existingPid=`getExistingPIDs $selfPID "$scriptFile"`

if [ ! -z "$existingPid" ]; then
  echo "The script already running, killing it..."
  kill -9 "$existingPid" #此方法有一定风险，慎用！
fi

doItsTask

}

#获取除本身进程以外其它运行当前脚本的进程的 PID
getExistingPIDs(){
selfPID="$1"
scriptFile="$2"
ps -ef | grep "/usr/bin/ksh ${scriptFile}" | grep -v "grep" | awk "{ if(\$2!=$selfPID) print \$2 }"
}

doItsTask(){
echo "Task is now being executed..."
sleep 20  #睡眠 20s，以模拟脚本在执行需要长时间完成的任务
}

main $*
回页首
脚本调试技巧
虽然 Shell 开发的一个普遍问题是调试困难，缺乏有效的调试工具。但是，我们可以采取一些能够一定程度上帮助我们规避调试困难的开发与调试的方式。 由于是脚本开发，不少人习惯于从直接地一行行地写代码，一个脚本里面甚至于一个函数都没有。虽然这种方式在语法上和功能上并无问题。但这增加了调试的难度。相反，如果采用模块化的方式去编写脚本，则使代码结构清晰、便于调试。这点，可以看这样一个例子。
假设下面的脚本的功能是收集生产环境中的相关日志文件，用于定位问题。需要收集的日志文件包括操作系统日志、中间件日志以及应用系统本身的日志。这些文件会被压缩成一个 gz 文件。
清单 5. 自动收集日志文件
#!/usr/bin/ksh

main(){
collectSyslog #收集系统日志文件
collectMiddlewareLog #收集中间件日志文件
collectAppLog #收集应用系统日志文件
tar -zcf logs.tgz syslog.zip mdwlog.zip applog.zip #将三中类型的日志打包，方便下载
}
若脚本执行报如下错误:
tar: applog.zip: Cannot stat: No such file or directory
我们可以很快锁定 collectAppLog 这个函数。因为它负责输出 applog.zip 这个文件。而没有必要看代码中的其它部分。
采用模块化的方式的另一个好处是代码调试的结果可以巩固下来。比如上面的例子中，如果你已经调试好了操作状态日志收集的函数。接下来调试其它函数的时候，这些被调试的代码尽管可能需要改动。但是这些改动影响到之前已经调试好的代码的可能并不大。相反，若是一个脚本中通篇都是语句，而不带函数，则改动其中一行代码，收集三种日志的功能可能都受影响。
另外一个典型的场景是脚本编写过程中，我们可能会因为不太确定一些问题如何处理而写一些尝试性的代码。然后，通过反复的调试去确认正确的处理方式。而事实上这些尝试性的代码可能就是一条语句甚至一个命令。但不少人是在大段的代码中反复去调试这一小段代码。这将非常耗时间。尤其是调试过程中代码中的其它部分调试时出现错误时，作者还得先解决其它错误，否则可能会时我们真正要调试的代码无法被执行到。这种情形下，专门写一个测试性的小脚本。
在该脚本中调试还我们不太确定该如何写的代码，如何将其”集成”到我们正在开发的脚本中。这样可以提高调试效率，避免消耗本不该消耗的时间。比方说，我们在编写过程中需要获取脚本本身所在进程的进程 ID。而此时我们又不太确定这个获取当前进程 id 的代码该怎么写。那么，我们可以新建一个测试性的脚本在其中尝试实现这个获取进程 ID 的功能。找到正确的方法后，将代码“移植”到我们真正要开发的脚本中。
回页首
处理大段字符输出
脚本开发中经常要处理的一个问题是输出提示信息。当然，对于简短的提示信息输出，使用 echo 命令就足够了。但是，对于大段的提示信息输出仍然使用 echo 命令处理则显得不够优雅。一种更适合的方法是使用 cat 命令结合输入重定向。下面通过一个具体例子来说明这点。
假设下面的脚本会将某个程序安装到用户指定的目录下。若用户指定的目录不存在，则提示
用户检查指定的目录是否正确，并重新执行脚本。
清单 6. 使用 echo 命令输出大段字符
#!/usr/bin/ksh

path="$1"

if [ ! -d "$path" ]; then
        #这里还必需处理星号这个特殊字符的显示
echo '****************************************************' 
echo ERROR
echo "The destination directory not exists,make sure below directory you specified is correct:"
echo ${path}
echo "Then re-run this script."
echo '****************************************************'
fi
这种方式的代码可读性不是很好，阅读者需要阅读多个 echo 命令然后再进行"综合"才能准确理解提示信息是什么。另外，一旦提示信息需要改动。这种改动可能因为改动其中一个 echo 命令时不小心多了一个双引号等特殊字符而引起语法错误，从而影响了整个脚本的执行。
清单 7 的代码则展示了如何使用 cat 命令和输入重定向来更好地处理大段文本的输出。
清单 7. 使用 cat 命令输出大段字符
#!/usr/bin/ksh

path="$1"
if [ ! -d "$path" ]; then
cat<<EOF
****************************************************
ERROR
The destination directory not exists,make sure below
directory you specified is correct:
${path}
Then re-run this script.
****************************************************
EOF
fi
显然，这种处理方式的代码更加简洁，可读性更好。阅读者只需要看一条命令，就知道提示信息的具体内容。并且，若要修改提示语，我们可以放心地在两个文件终止符 EOF 之间的部分改。即便修改错了，也不会影响到代码中的其它部分。
回页首
避免使用非必要的临时文件
新手在编写 Shell 脚本时往往在不必要使用临时文件的情况下使用了临时文件。这不仅增加了而外的代码编写工作量（用于处理创建、读取、和删除临时文件等），而且可能使脚本运行速度变慢（文件 I/O 毕竟不是快的操作）。
下面的例子中假设有个脚本的功能是往当前目录下所有的.txt 文件中添加如下一行文本：
--End of file name--
清单 8.和清单 9.中的代码分别显示了在不必要使用临时文件的情况下使用临时文件的代码和不需要使用临时文件的代码。
清单 8. 在不必要使用临时文件的情况下使用临时文件
#!/usr/bin/ksh

ls -lt *.txt | awk '{print $NF}' > tmp #将命令输出重定向到临时文件 tmp

cat tmp

typeset fileName

typeset lastLine

while read fileName #逐行读取临时文件中的每一行

do

 lastLine=`tail -1 "$fileName"`

 if [ ! "$lastLine" == "--End of $fileName--" ]; then

   echo "--End of $fileName--" >> $fileName

 fi

done <tmp #从临时文件进行输入重定向



rm tmp #删除临时文件
清单 9. 不使用临时文件
#!/usr/bin/ksh

typeset fileName

typeset lastLine

for fileName in $(ls -lt *.txt | awk '{print $NF}')

do

 lastLine=`tail -1 "$fileName"`

 if [ ! "$lastLine" == "--End of $fileName--" ]; then

   echo "--End of $fileName--" >> $fileName

 fi

done
回页首
使用支持 FTP 功能的编辑器
如果你的开发环境是在 Windows 操作系统下，而测试则是通过终端软件(如 Putty)在 Linux上进行。这种情形下，不少开发者习惯于在终端软件上直接编辑脚本(如使用 vi 命令)。显然，这种方式编辑效率低下。并且，脚本开发往往需要边修改边测试。即使是一个语法错误，由于缺乏工具的支持，我们可能要通过运行脚本才能发现。因此，提高脚本编辑效率某种程度上便提高了开发效率。在 Windows 系统上开发脚本时提高脚本编辑效率的一个不错的选择是使用支持简单的 FTP 功能的编辑器，如 Editplus 和 UltraEditor。可以使用这些编辑器以 FTP 的方式“打开”(实际上就是下载)Linux 测试主机上的脚本文件。编辑好脚本后对脚本进行保存时，这些编辑器会自动将脚本上传到测试主机上。接下来只需通过终端软件对脚本进行测试。如果测试后脚本需要继续修改，则可以利用编辑器的“重新载入文档”的功能(通常可以为该功能设置快捷键)。
