#### 1.显示所有分支

```
git branch -a
```

#### 2.显示远程仓库地址

```
git remote -v
```

#### 3.创建本地分支并跟踪远程分支

```
git checkout -b [分支名] [远程名]/[分支名]。
```

#### 4.取消对文件的修改。还原到最近的版本，废弃本地做的修改。

```
git checkout -- <file>
```

#### 5.取消已经暂存的文件。即，撤销先前"git add"的操作

```
git reset HEAD <file>...
```

#### 6.修改最后一次提交。用于修改上一次的提交信息，或漏提交文件等情况。

```
git commit --amend
```

#### 7.回退所有内容到上一个版本
```
git reset HEAD^
```

#### 8.回退a.py这个文件的版本到上一个版本

```
git reset HEAD^ a.py  
```

#### 9.向前回退到第3个版本

```
git reset –soft HEAD~3  
```

#### 10.将本地的状态回退到和远程的一样 

```
git reset –hard origin/master  
```

#### 11.回退到某个版本  

```
git reset 057d  
```

#### 12.回退到上一次提交的状态，按照某一次的commit完全反向的进行一次commit.(代码回滚到上个版本，并提交git)

```
git revert HEAD
```

#### 13.git记住密码

```
git config --global credential.helper store
```

#### 14.merge带选项--squash

```
git merge --squash
```

我们应该优先选择使用--squash选项，如下：

```
$ git merge --squash another
$ git commit -m "message here"
```

`--squash` 选项的含义是：本地文件内容与不使用该选项的合并结果相同，但是不提交、不移动HEAD，因此需要一条额外的commit命令。其效果相当于将another分支上的多个commit合并成一个，放在当前分支上，原来的commit历史则没有拿过来。

Note:

    判断是否使用--squash选项最根本的标准是，待合并分支上的历史是否有意义。

如果在开发分支上提交非常随意，甚至写成微博体，那么一定要使用-`-squash`选项。
