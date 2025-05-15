# **git学习笔记**
## **1.安装和初始化**
```bash
git -version # 查看版本号信息
git config --global user.name "fengliangnight" # 配置用户名 其中，--global表示全局配置，对所有仓库有效  使用最多
                                                              # --system表示系统配置，对所有用户有效 一般不会使用system
                                                              # 缺省（local）表示本地配置，秩对本地仓库有效
git config --global user.email username@mail.com # 配置邮箱 
                                                 # 这两个命令只需要执行一次
git config --global credential.helper store # 保存用户名和密码，这样就不用每次都输入了
git config --global --list # 参看git的配置信息
```
## **2.新建仓库**
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
|工作区||暂存区||本地仓库| 
|-|-|-|-|-|
|.git所在的目录|<span style="color:green">$\xrightarrow{\text{git add}}$</span>|.git/index|<span style="color:green">$\xrightarrow{\text{git add}}$</span>|.git/objects|
  
git中的文件也存在几种状态  
|未跟踪(Untrack)|未修改(unmodified)|已修改(Modified)|已暂存(staged)|
|-|-|-|-|
|新创建的还没有被git管理起来的文件|已经被git管理起来，但是文件内容没有变化|已经修改了文件，但是还没有添加到暂存区里面|已经修改了并且已经添加到暂存区里的文件|
            
## **4 添加和提交文件**
|git init|创建仓库|
|-|-|
|git status|查看仓库状态|
|-|-|
|git add|添加到暂存区|
|-|-|
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
