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

补充
|文件状态|含义|
|-|-|
|??(Untracked)|未跟踪|
|M(Modified)|已修改|
|A(Added)|已添加暂存|
|D(Deleted)|已删除|
|R(Renemed)|重命名|
|U(Updated)|已更新未合并|
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
    
[提供了各种常见语言的忽略文件模板](https://github.com/github/gitignore)
    
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
    
|本地仓库(Local Repo)|$\longleftarrow$```git clone```|远程仓库(Remote Repo)|
|-|-|-|
||$\longleftarrow$```git pull```(把远程仓库的修改拉取到本地仓库)||
||$\longrightarrow$```git push```(把本地仓库的修改推送给远程仓库)||
    
## **10 关联本地仓库和远程仓库**
**step 1**  
```git remote add <远程仓库别名> <远程仓库地址>```
**step 2**  
```git push -u <远程仓库名> <分支名>```
```bash
# 现在github上创建好一个新的仓库 例如 first-repo
# 本地有个my-repo的本地仓库
cd my-repo
# git remote add <shortname> <url> # 添加一个远程仓库
git remote add origin git@github.com:fengliangnight/first-repo.git # origin 远程仓库的别名，一般默认的别名都是这个
git remote -v # 查看当前仓库所对应的远程仓库的别名和地址
    # origin  git@github.com:fengliangnight/first-repo.git (fetch)
    # origin  git@github.com:fengliangnight/first-repo.git (push)
# git branch -M main # 指定分支的名称为main 由于我们默认的分支名称就是main，这一行可以省略
git push -u origin main # 把本地的main分支和远程仓库的main分支关联起来 
                        # 全称应当为 git push -u origin main:main 
                        # -u 是upstream的缩写，意思是把本地仓库与别名为origin的远程仓库关联起来
                        # main:main 把本地仓库的main分支推送给远程仓库的main分支
                        # 本地分支为master -> git push -u origin master:main
```
```git pull <远程仓库名> <远程分支名>:<本地分支>```
```bash
git pull origin main # 仓库名称 和 分支名称 可省略 默认就是拉取仓库别名为origin的main分支
                     # 作用 将远程仓库的指定分支拉取到本地在合并
```
执行```git pull```需要注意的是在执行完```git pull```之后，git会自动为我们执行以此合并操作，如果远程仓库中的修改内容和本地仓库中的修改内容没有冲突的话，那么合并操作就会成功，否则就会失败，这是需要手动解决以下冲突   
从远程仓库获取内容还可使用```git fetch```，区别在于，```git fetch```只是获取远程仓库的修改，但是并不会自动合并到本地仓库中，而是需要我们手动合并  

## **11 [Gitee](gitee.com)的使用和[GitLab](gitlab.com)的本地化部署**
省略
## **12 分支简介和基本操作** 
```bash
mkdir brach-demo
cd brach-demo
git init 
# 为方便 使用 分支名+序号 命名文件 
# 分支名+冒号+序号 编写提交记录
echo main1 > main1.txt
git add .
git commit -m "main:1"
echo main2 > main2.txt
git add .
git commit -m "main:2"
echo main3 > main3.txt
git add .
git commit -m "main:3"
git branch # 查看当前仓库的所有分支
    # * master   # 命令行中带有 * 就是目前所处在的分支
git branch dev # git branch <分支名>    创建新的分支 但没有切换到这个分支上
git branch 
    #   dev
    # * master
git checkout dev # git checkout <分支名>   切换到不同的分支
                 # 但要注意git checkout 不仅有 切换分支 的作用
                                       # 还有 恢复文件 的作用
                 # 当文件名与分支名相同时，就会产生歧义，git checkout 默认切换分支，而不是恢复文件
                 # 为了避免这种歧义，git官方在2.23版本开始为我们提供了一个新命令
                         # git switch <分支名>
git switch master 
git switch dev
echo dev1 > dev1.txt
git add .
git commit -m "dev:1"
echo dev2 > dev2.txt
git add . 
git commit -m "dev:2"
ls 
    # dev1.txt  dev2.txt  main1.txt  main2.txt  main3.txt
git switch master 
ls 
    # main1.txt  main2.txt  main3.txt
echo main4 > main4.txt
git add .
git commit -m "main:4"
echo main5 > main5.txt
git add .
git commit -m "main:5"
git merge dev # git merge <将要合并的分支>   当前所在的分支就是合并后的目标分支
git log --graph --oneline --decorate --all # 查看分支图
git branch
    #   dev
    # * master
git branch -d dev # 删除已经完成合并的分支 
                  # 如果没有合并的话 就不能使用 -d 参数 删除分支
                  # git branch -D <branch-name>  删除没有合并的分支
```

## **13 解决合并冲突**
``` bash
git branch feat
git switch feat
ls
vi main1.txt
    # 添加内容： 这是feat分支中添加的内容
git commit -am "feat:1"
git switch master 
cat main1.txt
vi main1.txt
    # 添加内容： 这是master分支中添加的内容
git commit -am "main:6"
git merge feat
    # 自动合并 main1.txt
    # 冲突（内容）：合并冲突于 main1.txt
    # 自动合并失败，修正冲突然后提交修正的结果。 
git status
    # 自动合并 main1.txt
    # 冲突（内容）：合并冲突于 main1.txt
    # 自动合并失败，修正冲突然后提交修正的结果。
    # root@dsw-1079730-5498c98d5b-gzgnj:/mnt/workspace/brach-demo# git status
    # 位于分支 master
    # 您有尚未合并的路径。
    # （解决冲突并运行 "git commit"）
    #  （使用 "git merge --abort" 终止合并）

    # 未合并的路径：
    #  （使用 "git add <文件>..." 标记解决方案）
    #        双方修改：   main1.txt

    # 修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
git diff    
    # diff --cc main1.txt
    # index 9931141,849af09..0000000
    # --- a/main1.txt
    # +++ b/main1.txt
    # @@@ -1,2 -1,2 +1,6 @@@
    #   main1
    # ++<<<<<<< HEAD
    #  +这是master分支中添加的内容
    # ++=======
    # + 这是feat分支中添加的内容
    # ++>>>>>>> feat
vi main1.txt
    # 手动修改
git add .
git commit -m "merge conflict"
# 如果想终端这次合并的话 可以使用 
# git merge --abort
```

## **14 回退和Rebase**
```git rebase <branch-name>```
可以在任意分支上执行**rebase**操作

```bash
git branch -d feat
git checkout -b dev 0fb1fc8 # 将dev分支恢复到合并之前的状态
git log --oneline --graph --decorate --all # 小窍门 可以使用 alias 命令将其定义成一个别名
alias graph="git log --oneline --graph --decorate --all"
graph
git switch master
git reset --hard  1ad9d54 # 将master分支也回退到合并之前的状态
cd ..
cp -rf branch-demo rebase1
cp -rf branch-demo rebase2
cd rebase1
git switch dev 
git rebase main # 将当前的dev分支变基到目标的main分支上
cd ../rebase2
git rebase dev # 将当前分支main变基到目标分支dev上
```
    
**rebase**与**merge**有什么区别，该如何区分使用
||```merge```|```rebase```|
|-|-|-|
|优点|不会破坏缘分支的提交历史，方便回溯和查看|不会新增额外的提交记录，形成线性历史，比较直观和干净|
|缺点|会产生额外的提交节点，分支图比较复杂|会改变提交历史，改变了当前分支branch out的节点，避免在共享分支中使用，一般不会在公共的分支上执行rebase操作|

## **15 分支管理和工作流模型**
[**Gitflow工作流模型**](https://zhuanlan.zhihu.com/p/389877465)
    