---
title: Git配置及常用命令--干货版
date: 2016-08-19 23:32:07
tags:
---
### Git安装与配置
#### Linux下安装与配置
1.  通过命令行 `sudo apt-get install git` 进行安装
2.  配置 `git config --global user.name "xxx"`, `git config --global user.email "xxx@xxx.com"` (global 选项可选)
3.  使用 git push到github时，需要在github上添加本机的SSH key
>  - 生成SSH Key： `ssh-keygen -t rsa -C "xxx@xxx.com"`  (按三个回车即可，一般不使用密码)
> -  生成的key在 `～/.ssh` 目录下，该目录下包含两个文件:  `id_ras`（私钥）， `id_rsa.pub`（公钥）
> - 添加私钥：`ssh-add id_rsa`
> - 在github上添加ssh key. 在 setting->SSH and GPG Keys下添加 `id_ras.pub` 中内容
#### windows下安装与配置  
1. 直接下载安装 [git](https://git-scm.com/downloads)
2. 配置与linux 下类似

**说明：** git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址.

### 常见操作命令
#### 创建版本库
**`git init`**  : 创建版本库
**`git add .`** : 添加文件到版本库，“.”表示目录下所有文件，也可通过文件名或目录指定
**`git rm xxx`**: 从版本库中删除一个文件
**`git commit -m "xxx"`**: 提交文件，-m选项表示的示提交说明

#### 查看文件改动
**`git status`**: 查看工作区文件变动情况，显示那些文件被修改过
**`git diff xxx`**: 查看文件xxx的修改内容
**`gitk`**:  图形化界面各版本修改情况

#### 版本回退
**`git log`**: 查看提交历史，由近到远排列，**`git log --pretty=oneline`** 简化输出信息
**`git reset --hard xxx`**:  xxx 为 commit id，回退到指定commit id 的版本
**`git reflog`**： 查看历史命令，以便确定回到未来哪个版本

#### 撤销单个文件的修改
若文件没有执行 add 操作
**`git checkout -- xxx`** :  直接丢弃工作区文件xxx的修改
若文件执行过add 操作, 需执行两步
**`git reset HEAD xxx`**  --> **`git checkout -- xxx`**

#### 关联远程仓库与本地仓库
首先需要在github中手动新建分支
一种方式是将分支 clone 到本地，进行修改，然后提交。
**`git clone git@github.com:username/RepositoryName.git`**
另一种方式是，讲本地已有仓库与之关联。
**`git remote add origin git@github.com:username/RepositoryName.git`**
提交本地库内容到远程库
**`git push -u origin master`**: 将当前master分支推送当远程分支origin, `-u`参数，表示将本地master分支与远程的origin分支关联起来，在以后的推送或者拉取时，可以简化命令`git push origin master`
**`git remote -v`**:查看远程库信息
**`git checkout -b branch-name origin/branch-name`**在本地创建和远程分支对应的分支,本地和远程分支的名称最好一致
**`git branch --set-upstream branch-name origin/branch-name`**:建立本地分支与远程分支的关联

**`git pull`**:从远程抓取分支，每次提交本地库到远程库之前执行，如有冲突，先处理冲突

#### 分支操作
**`git checkout -b dev`**:创建并切换到分支dev
**`git branch`**:查看当前分支
**`git checkout master`**: 切换到分支master
**`git merge dev`**: 将dev分支的工作成果合并到master(合并指定分支到当前分支)
**`git branch -d dev`**: 删除分支dev
**`git branch -D dev`**: 强制删除分支dev
Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，当合并分支出新冲突时，用git status查看具体情况，再手动修改合，排除冲突后进行合并。
**`git log --graph`**:命令可以看到分支合并图
**`--no-ff`**:合并分支时使用此参数，合并后的历史有分支，可以看出来那些曾经修改过。
**`git stash`**:储存当前工作现场，使用**`git stash list`**查看存储的工作现场，使用**`git stash applay`**恢复（需要使用`git stash drop删除stash内容`）， 或者使用**`git stash pop`**恢复的同时将stash的内容删除

#### 标签操作
**`git tag <name>`**用于新建一个标签，默认为HEAD，也可以指定一个commit id；
**`git tag -a <tagname> -m "blablabla..."`**可以指定标签信息；
**`git tag -s <tagname> -m "blablabla..."`**可以用PGP签名标签；
**`git tag`**可以查看所有标签。
**`git push origin <tagname>`**可以推送一个本地标签；
**`git push origin --tags`**可以推送全部未推送过的本地标签；
**`git tag -d <tagname>`**可以删除一个本地标签；
**`git push origin :refs/tags/<tagname>`**可以删除一个远程标签


#### 其他
**`git config --global color.ui true`**:让Git显示颜色，会让命令输出看起来更醒目
**.gitignore**忽略特殊文件，用户可以修改指定忽略的文件


如有疑问和建议，欢迎指导和提问。


参考资料：推荐[廖雪峰git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

