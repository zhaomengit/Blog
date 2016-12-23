
#### 1.第一步(可省略,但最好不要省略,不然生成的git log提交记录用户名就随机生成了)

从要转化为git的项目的目录下获取svn上用户信息,对应成git用户的形式
    ```
    svn log --xml | grep author | sort -u |   perl -pe 's/.*>(.*?)<.*/$1 = $1 <$1\@email.com>/' > user.txt
    ```
文件中内容是这样:

```
xxx = xxx <xxx@email.com>
yyy = yyy <yyy@email.com>
```

详细说明见连接: https://git-scm.com/book/zh/v2/Git-%E4%B8%8E%E5%85%B6%E4%BB%96%E7%B3%BB%E7%BB%9F-%E8%BF%81%E7%A7%BB%E5%88%B0-Git

#### 2.第二部执行转化命令

```
git svn clone %your_svn_address% --authors-file=user.txt  --no-metadata  -r {FROM}:{TO} -s %project_name%
```

注意事项:

1. %your_svn_address%:svn地址
2. user.txt是上一步生成的user.txt
3. --no-metadata 不要包括 Subversion 通常会导入的元数据
4. -r 选项是从svn的那个提交记录开始转为git log,如果svn工程比较大,较快的执行转化,选一个恰当的时间点,例如: 1234:HEAD 从1234提交记录到最新的HEAD
5. -s 命令,这个要注意下,解释如下:

```
使用命令git svn clone -s %your_svn_address%, 为什么要加-s ， 因为这个仓库是按照标准的tags、trunk、branches结构划分的， 
则git svn会将对应的分支、标签识别存放到git的结构中。 如果不加-s，不会将tags、branch按照git的结构进行划分 。
```

如果你加入`-s`命令生成的项目为空,说明svn仓库不是按照标准的tags、trunk、branches结构划分的,去掉`-s`选项

6. %project_name% 要放置项目的文件夹


如果mac上遇到如下问题:

```
Can't locate SVN/Core.pm in @INC (you may need to install the SVN::Core module) (@INC contains: /usr/local/git/lib/perl5/site_perl/5.18.2/darwin-thread-multi-2level /usr/local/git/lib/perl5/site_perl/5.18.2 /usr/local/git/lib/perl5/site_perl /Library/Perl/5.18/darwin-thread-multi-2level /Library/Perl/5.18 /Network/Library/Perl/5.18/darwin-thread-multi-2level /Network/Library/Perl/5.18 /Library/Perl/Updates/5.18.2 /System/Library/Perl/5.18/darwin-thread-multi-2level /System/Library/Perl/5.18 /System/Library/Perl/Extras/5.18/darwin-thread-multi-2level /System/Library/Perl/Extras/5.18 .) at /usr/local/git/lib/perl5/site_perl/Git/SVN/Utils.pm line 6.
BEGIN failed--compilation aborted at /usr/local/git/lib/perl5/site_perl/Git/SVN/Utils.pm line 6.
Compilation failed in require at /usr/local/git/lib/perl5/site_perl/Git/SVN.pm line 25.
BEGIN failed--compilation aborted at /usr/local/git/lib/perl5/site_perl/Git/SVN.pm line 32.
Compilation failed in require at /usr/local/git/libexec/git-core/git-svn line 21.
BEGIN failed--compilation aborted at /usr/local/git/libexec/git-core/git-svn line 21.
```

解决方式
```
sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi-2level/SVN/ /Library/Perl/5.18/SVN
sudo mkdir /Library/Perl/5.18/auto
sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi-2level/auto/SVN/ /Library/Perl/5.18/auto/SVN
```

#### 3.将标签变为合适的 Git 标签，运行,(如果没有指定 -s选项,这部可省略)

```
cp -Rf .git/refs/remotes/origin/tags/* .git/refs/tags/
rm -Rf .git/refs/remotes/origin/tags
```

这会使原来在 remotes/origin/tags/ 里的远程分支引用变成真正的（轻量）标签。

接下来，将 refs/remotes 下剩余的引用移动为本地分支：

```
cp -Rf .git/refs/remotes/* .git/refs/heads/
rm -Rf .git/refs/remotes
```

#### 4.增加远程分支

前提条件是你在gitlab中已经建立了git项目,获得git仓库地址

```
git remote add origin %your_git_repo%
git push origin --all
```

注意: git push命令默认你已经本地保存好了用户名和密码,如果git push失败,

修改文件加下.git/config文件,在`[remote "origin"]`中url增加下用户名和密码再提交

```
[remote "origin"]
	url = http://{your_user}:{your_passwd}@{your_git_repo}
```
