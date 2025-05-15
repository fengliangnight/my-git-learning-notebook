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
|git commit|提交|

```bash
# 示例
cd my-repo 
git status
echo "这是第一个文件" > file1.txt
ls
cat file1.txt
git 