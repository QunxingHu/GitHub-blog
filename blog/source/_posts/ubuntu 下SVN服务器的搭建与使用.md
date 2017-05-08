---
title: ubuntu下svn服务器的搭建与使用
date: 2017-5-7 10:16:23 AM
categories:
	- 术业专攻
tags: 
  - svn
  - linux
---
由于需要将 linux 下的项目转移到 windows 平台进行编译，linux 平台下项目使用的 git + repo 进行管理的， Windows下不支持 repo, 所以动手搭建了 svn 服务器， 记录过程如下。
<!-- more -->

# SVN 服务器的搭建与使用

由于需要将 linux 下的项目转移到 windows 平台进行编译，linux 平台下项目使用的 git + repo 进行管理的， Windows下不支持 repo, 所以动手搭建了 svn 服务器， 记录过程如下。

## Ubuntu 上搭建 SVN 服务器

1. 安装 svn

`sudo apt-get install subversion`

2. 新建目录，作为 svn 根目录
`mkdir ~/svn`

3. 创建数据库
`svnadmin create ~/svn/Demo`
创建 Demo 数据库

4. 配置数据库
- `cd ~/svn/Demo && ls`
可以发现目录下已经生成 `conf db format hooks locks README.txt` 文件和文件夹。conf文件夹下主要是一些配置文件，db文件夹存储的是svn转储后的数据。
- conf 目录下文件说明

```
conf/svnserve.conf 主配置文件
conf/authz 用户权限文件
conf/passwd　 存储用户和密码的文件
```

- 配置版本库： `vim conf/svnserve.conf` 将以下参数去掉注释

```
#匿名用户访问权限，默认read，none为不允许匿名用户访问
anon-access = none
#认证用户权限 可写
auth-access = write
#密码文件为passwd（默认在版本库/conf下面，也可以绝对路径指定文件位置）
password-db = passwd
#权限文件为authz （默认在版本库/conf下面，也可以绝对路径指定文件位置）
authz-db = authz
```

- 配置用户名和密码： `vim conf/passwd`

```
#前面是用户名，后面是密码
[users]
lilei = 123456
jack = 123456
wang = 123456
```

- 配置用户组和用户组成员权限： `vim conf/authz`

```
[groups]
#定义用户组，以及用户组下面的成员
manager = lilei
guest = jack,wang
#manager用户组下面的成员对以根目录起始的Demo版本库具有读写权限
[Demo:/]
@manager = rw
#guest用户组下面的成员对Demo版本库下media目录只读权限
[ZYQ:/media]
@guest = r
```

5. 启动 SVN 服务

```
sudo svnserve -d -r ~/svn/Demo
ps aux | grep svnserve 查看进程是否启动
sudo killall svnserve 关闭 SVN 服务
```

6. 导入项目
将已有文件或文件夹导入到 Demo 项目中
`svn import -m "First import" ~/learn/ svn://localhost/Demo`

7. 其他客户端的使用就不详细介绍了

8. 踩过的坑
 - windows 对文件名的大小写不敏感，同名文件会直接覆盖，注意文件名大小写问题