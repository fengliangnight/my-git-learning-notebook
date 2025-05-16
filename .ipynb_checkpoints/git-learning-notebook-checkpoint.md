# **git学习笔记**
## **1 安装和初始化**
```bash
git -version # 查看版本号信息
git config --global user.name "your_name" # 配置用户名 其中，--global表示全局配置，对所有仓库有效  使用最多
                                                              # --system表示系统配置，对所有用户有效 一般不会使用system
                                                              # 缺省（local）表示本地配置，秩对本地仓库有效
git config --global user.email "your_email@example.com" # 配置邮箱 
                                                 # 这两个命令只需要执行一次
git config --global credential.helper store # 保存用户名和密码，这样就不用每次都输入了
git config --global --list # 参看git的配置信息
```
## **2 新建仓库**
创建仓库  
方式一  
```bash
git init  
```
方式二  
```bash
git clone  
```
## **3 工作区域和文件状态**
git 的本地数据管理分为三个区域：  
工作区(Working Directory)  
暂存区(staging Area/Index)  
本地仓库(Local Repository)  
|工作区|     |暂存区|     |本地仓库| 
|-|-|-|-|-|
|.git所在的目录|<span style="color:green">```git add```|.git/index|<span style="color:green">```git commit```</span>|.git/objects|
  
git中的文件也存在几种状态  
|未跟踪(Untrack)|未修改(unmodified)|已修改(Modified)|已暂存(staged)|
|-|-|-|-|
|新创建的还没有被git管理起来的文件|已经被git管理起来，但是文件内容没有变化|已经修改了文件，但是还没有添加到暂存区里面|已经修改了并且已经添加到暂存区里的文件|
            
## **4 添加和提交文件**
|git init|创建仓库|
|-|-|
|git status|查看仓库状态|
|git add|添加到暂存区|
|git commit|提交(只会提交暂存区中的文件，不会提交工作区中的文件)|

```bash
# 示例
cd my-repo 
git status
echo "这是第一个文件" > file1.txt
ls
cat file1.txt
git status
git add file1.txt
echo "这是第二个文件" > file2.txt
git status
git commit -m "first commit" # 使用-m参数指定提交的信息，这个信息会被记录到仓库中 因此，file2.txt是不会被提交的仓库里面的
echo "file3" > file3.txt
echo "file4" > file4.txt
echo "file5" > file.sh
git status
git add *.txt # 将所有以txt结尾的文件添加到暂存区中
git add . # 把当前文件夹中所有文件添加到暂存区中
git commit -m "第二次提交"
git log # 查看提交记录
git log --oneline # 查看简洁的提交记录
```
 
## **5 git reset回退版本**
git reset的三种模式  
||工作区|暂存区|
|-|-|-|
|git reset --soft|√|√|
|git reset --hard|×|×|
|git reset --mixed|√|×|


```bash
# 示例
git init repo
cd repo
echo 111 > file1.txt
echo 222 > file2.txt
echo 333 > file3.txt
git add file1.txt
git commit -m "commit1"
    # [master （根提交） e64299e] commit1
    # 1 file changed, 1 insertion(+)
    # create mode 100644 file1.txt
git add file2.txt
git commit -m "commit2"
git add file3.txt
git commit -m "commit3"
git log --oneline
    # 9845aa4 (HEAD -> master) commit3
    # af4d24a commit2
    # e64299e commit1
cd ..
cp -rf repo repo-soft
cp -rf repo repo-hard
cp -rf repo repo-mixed
cd repo-soft
git reset --soft af4d24a # 后面加上要回退的版本ID
git log --oneline
    # af4d24a (HEAD -> master) commit2
    # e64299e commit1
ls # file3.txt文件还在
cat file3.txt # 查看file3.txt的内容也是没问题的
git ls-files # 查看暂存区的内容 file3.txt在暂存区也是还在
git status
    # 位于分支 master
    # 要提交的变更：
    #  （使用 "git restore --staged <文件>..." 以取消暂存）
    #        新文件：   file3.txt
cd ../repo-hard
git reset --hard HEAD^ # HEAD^表示上一个版本
git log --oneline
ls # file3.txt已经不存在了
git ls-files # 暂存区里也没有file3.txt
cd ../repo-mixed
git reset HEAD^ # 默认 mixed
ls # 工作区有file3.txt
cat file3.txt
git ls-files # 暂存区里已经没有file3.txt
```

git 中的所有操作都是可以回溯的  
```git reflog```查看我们操作的历史记录  
找到误操作之前的版本号  
```git reset --hard 版本号```回退到这个版本

## **6 使用git diff查看差异**
|```git diff```|可以用来查看文件在工作区 暂存区以及版本库之间的差异|
|-|-|
||还可以查看文件在两个特定版本之间的差异|
||文件在两个分支之间的差异|
    
```git diff```如果什么都不加的话默认比较的是工作区与暂存区之间的差异，它会显示更改的文件以及更改的详细信息
```bash
# 示例
git init repo
cd repo
echo 111 > file1.txt
echo 222 > file2.txt
echo 333 > file3.txt
git add file1.txt
git commit -m "commit1"
git add file2.txt
git commit -m "commit2"
git add file3.txt
git commit -m "commit3"
git log -oneline
    # ad3e1f2 (HEAD -> master) commit3
    # 477b262 commit2
    # 3b39300 commit1
vi file3.txt # 将文件内容改成  three
git diff
    # diff --git a/file3.txt b/file3.txt
    # index 55bd0ac..1d19714 100644
    # --- a/file3.txt
    # +++ b/file3.txt
    # @@ -1 +1 @@
    # -333
    # +three
    # \ No newline at end of file
git add .
git diff # 没有任何内容
git diff HEAD # 比较 工作区 和 版本库 之间的差异
    # diff --git a/.ipynb_checkpoints/file3-checkpoint.txt b/.ipynb_checkpoints/file3-checkpoint.txt
    # new file mode 100644
    # index 0000000..1d19714
    # --- /dev/null
    # +++ b/.ipynb_checkpoints/file3-checkpoint.txt
    # @@ -0,0 +1 @@
    # +three
    # \ No newline at end of file
    # diff --git a/file3.txt b/file3.txt
    # index 55bd0ac..1d19714 100644
    # --- a/file3.txt
    # +++ b/file3.txt
    # @@ -1 +1 @@
    # -333
    # +three
    # \ No newline at end of file
git diff --cached # 比较 暂存区 与 版本库 之间的差异
    # diff --git a/.ipynb_checkpoints/file3-checkpoint.txt b/.ipynb_checkpoints/file3-checkpoint.txt
    # new file mode 100644
    # index 0000000..1d19714
    # --- /dev/null
    # +++ b/.ipynb_checkpoints/file3-checkpoint.txt
    # @@ -0,0 +1 @@
    # +three
    # \ No newline at end of file
    # diff --git a/file3.txt b/file3.txt
    # index 55bd0ac..1d19714 100644
    # --- a/file3.txt
    # +++ b/file3.txt
    # @@ -1 +1 @@
    # -333
    # +three
    # \ No newline at end of file
git commit -m "commit4"
git diff --cached # 没有任何内容
git diff HEAD # 也没有任何内容
              # 表示 工作区 暂存区 版本库 之间的内容是一致的
git log --oneline
    # 79114d8 (HEAD -> master) commit4
    # ad3e1f2 commit3
    # 477b262 commit2
    # 3b39300 commit1
git diff 477b262 ad3e1f2
    # diff --git a/file3.txt b/file3.txt
    # new file mode 100644
    # index 0000000..55bd0ac
    # --- /dev/null
    # +++ b/file3.txt
    # @@ -0,0 +1 @@
    # +333
git diff ad3e1f2 HEAD
git diff HEAD~ HEAD # HEAD~表示上一个版本
git diff HEAD^ HEAD # HEAD^也表示上一个版本
git diff HEAD~2 HEAD # HEAD~2表示HEAD之前的两个版本
git diff HEAD~3 HEAD file3.txt # 在后面加上文件名 就是会查看对应文件的差异内容
```
## **7 使用git rm删除文件**
```bash
git init my-repo
cd my-repo
echo 111 > file1.txt
echo 222 > file2.txt
echo 333 > file3.txt
echo 444 > file4.txt
echo 555 > file5.sh
git add .
rm file1.txt
ls
git status
    # 位于分支 master

    # 尚无提交

    # 要提交的变更：
    #（使用 "git rm --cached <文件>..." 以取消暂存）
    #        新文件：   file1.txt
    #        新文件：   file2.txt
    #        新文件：   file3.txt
    #        新文件：   file4.txt
    #        新文件：   file5.sh

    # 尚未暂存以备提交的变更：
    #  （使用 "git add/rm <文件>..." 更新要提交的内容）
    #  （使用 "git restore <文件>..." 丢弃工作区的改动）
    #        删除：     file1.txt
git ls-files
    # file1.txt
    # file2.txt
    # file3.txt
    # file4.txt
    # file5.sh
# git add file1.txt
git add .
git ls-files
    # file2.txt
    # file3.txt
    # file4.txt
    # file5.sh
git rm -f file2.txt
git status
    # 位于分支 master

    # 尚无提交

    # 要提交的变更：
    #  （使用 "git rm --cached <文件>..." 以取消暂存）
    #        新文件：   file3.txt
    #        新文件：   file4.txt
    #        新文件：   file5.sh
ls
    # file3.txt  file4.txt  file5.sh
git commit -m "delete"
```
## **8 .gitignore忽略文件**
|.gitignore||
|-|-|
|√|忽略日志文件和文件夹|
|√|忽略所有.class文件|
|√|忽略所有.o文件|
|√|忽略所有.env文件|
|√|忽略所有.zip和.tar文件|
|√|忽略所有.pem文件|
||...|
    
**应该忽略哪些文件**
1. 系统或软件自动生成的文件
2. 编译产生的中间文件和结果文件
3. 运行时生成日志文件、缓存文件、临时文件
4. 涉及身份、密码、口令、密钥等敏感信息文件
```bash
# 示例
cd my-repo
echo "some log" > access.log
git status
    # 位于分支 master
    # 未跟踪的文件:
    #  （使用 "git add <文件>..." 以包含要提交的内容）
    #        access.log

    # 提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）
echo "other log" > other.log
echo access.log > .gitignore
cat .gitignore
git status
    # 位于分支 master
    # 未跟踪的文件:
    #  （使用 "git add <文件>..." 以包含要提交的内容）
    #        .gitignore
    #        other.log

    # 提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）
git add .
git status
git commit -m "ignore"
git ls-files
    # .gitignore
    # file3.txt
    # file4.txt
    # file5.sh
    # other.log
vi .gitignore
    # 加上一行 *.log
echo hello > hello.log
git status
    # 位于分支 master
    # 尚未暂存以备提交的变更：
    #  （使用 "git add <文件>..." 更新要提交的内容）
    #  （使用 "git restore <文件>..." 丢弃工作区的改动）
    #        修改：     .gitignore

    # 修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
git commit -am "test ignore log" # -am 参数，同时完成添加至暂存区和提交至仓库的两个操作
git ls-files
    # .gitignore
    # file3.txt
    # file4.txt
    # file5.sh
    # other.log
echo "modeified" >> other.log # >> 表示追加到文件的后面
git status
    # 位于分支 master
    # 尚未暂存以备提交的变更：
    #  （使用 "git add <文件>..." 更新要提交的内容）
    #  （使用 "git restore <文件>..." 丢弃工作区的改动）
    #        修改：     other.log

    # 修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
# git 还是注意到了other.log的变化
# .gitignore生效需要有一个前提： 这个文件不能是已经被添加到版本库中的文件
git diff
git rm --cached other.log # --cached 参数表示不想删除本地文件 而是将其中暂存区中删除
git commit -am "delete other.log"
echo "some change " >> other.log
git status
    # 位于分支 master
    # 无文件要提交，干净的工作区
mkdir temp # 如果文件夹为空，git默认不讲空文件夹纳入到版本控制中
           # 如果文件夹非空，就会被纳入版本控制中
git status
echo "hello" > temp/hello.txt
git status -s # -s 是short的缩写，表示查看状态命令的简略模式 
    # ?? temp/        # 第一列表示暂存区的状态 第二列表示工作区的状态
vi .gitignore
    # 添加 temp/    # 注意文件夹的格式是以斜线 / 结尾的
git status -s 
    #  M .gitignore      # M 表示.gitignore文件被修改过
git commit -am "test ignore folder"
git ls-files
    # .gitignore
    # file3.txt
    # file4.txt
    # file5.sh
```
**.gitignore文件的匹配规则**
从上到下逐行匹配，每一行表示一个忽略模式  [详细规则](https://git-scm.com/docs/gitignore)
1. 空行或以#开头的行会被git忽略，一般空行用于可读性的分割，#一般用作注释
2. 使用标准的glob模式匹配，例如：
    1. 星号 * 通配任意字符
    2. 问号 ? 匹配单个字符
    3. 中括号 [] 表示匹配列表中的单个字符，比如：[abc]表示a或b或c
3. 两个星号**表示匹配任意的中间目录
4. 中括号可以使用短中线连接，比如：
    [0-9]表示任意一位数字，[a-z]表示任意一位小写字母
5. 感叹号!表示取反
    
```bash
# 示例
# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a 即便你在前面忽略了 .a 文件
!lib.a

# 之忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为  build 的文件夹
build/

# 忽略 doc/notes.txt 但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```
    
[提供了了各种常见语言的忽略文件模板](https://github.com/github/gitignore)
    
## **9 SSH配置和克隆仓库**
```bash
# 配置SSH密钥
cd ~
cd .ssh
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" # -t 指定协议为RSA -b 指定生成的大小为4096
    # Generating public/private rsa key pair.
    # Enter file in which to save the key (/root/.ssh/id_rsa):      # 如果是第一次创建，直接回车就行，它会在~/.ssh目录下生成一个id_rsa的密钥文件
                                                                    # 输入 新的文件名（比如：test）
    # Enter passphrase (empty for no passphrase): 
    # Enter same passphrase again: 
    # Your identification has been saved in test
    # Your public key has been saved in test.pub
    # The key fingerprint is:
    # SHA256:GtxI8YYQooW6mtZ7Tqd1BK1XlyIoxL01T8Gt67a8iSs root@dsw-1079058-6d7cc4b567-jh54z
    # The key's randomart image is:
ls -ltr
    # -rw-r--r-- 1 root root  759  5月 16 11:43 test.pub       # 公钥文件，上传至github
    # -rw------- 1 root root 3454  5月 16 11:43 test           # 私钥文件, 不能泄露
vi test.pub
    # 复制公钥文件
```
将复制的公钥文件上传至github(settings -> SSH and GPG keys -> New SSH key)  
如果是第一次创建SSH密钥，而且在创建密钥的时候也没有修改过默认的文件名的话，SSH密钥的配置到这里就完成了  
如果是刚刚指定了一个文件名的话，还需增加一步配置  
```bash
tail -5 config # 创建一个config文件，并将下面五行内容添加到文件中
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/test
```
    
|本地仓库(Local Repo)|⬅git clone|远程仓库(Remote Repo)|
|-|-|-|
||⬅git pull(把远程仓库的修改拉取到本地仓库)||
||➡git push(把本地仓库的修改推送给远程仓库)||