---
title: docker 入门
date: 2017-5-22 10:38:58 AM
categories:
- 术业专攻
tags: 
- docker
---
因为公司项目较多，编译环境不尽相同，加之大部分研发人员需要参与不同的项目，切换开发环境比较麻烦，一不小心就导致收之桑榆，失之东隅了。因此我尝试使用docker为各项目建立编译环境镜像，用来维护开发环境的一致性。本篇博客主要介绍docker的入门知识，包含docker的安装，镜像维护，容器的基本操作。
<!-- more -->
# docker 入门

## 安装 docker

### 系统要求

docker 目前只支持64位平台，且内核版本需高于3.10， ubuntu 14.04, 16.04均满足要求。其他操作系统，请在安装前请核实当前主机的平台版本和内核版本
```
$ uname -a
Linux hqx-HP 4.4.0-75-generic #96-Ubuntu SMP Thu Apr 20 09:56:33 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```

### 安装

依次输入以下命令即可

* 获取支持 http 协议的源
    
    `$ sudo apt-get install apt-transport-https ca-certificates`

* 添加 gpg 密钥
    
    `$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D`

* 获取当前操作系统代号
    
    14.04 \(LTS\) 代号为 trusty，15.04 代号为 vivid，15.10 代号为 wily，16.04 代号为xenial

    `$ lsb_release -c`

* 添加软件源
    
    `$ sudo vim /etc/apt/sources.list.d/docker.list`
    
    添加下列内容: **注意** ubuntu-xenial 请修改为对应的操作系统代号
    
    `deb https://apt.dockerproject.org/repo ubuntu-xenial main`

* 更行缓存

    `$ sudo apt-get update`

* 安装 aufs 存储

    `$ sudo apt-get install linux-image-extra-$(uname -r) -y`

* 安装docker

    `$ sudo apt-get install docker-engine -y`

## 获取镜像

* 下载镜像
    * 从 docker hub上获取镜像

        `$ sudo docker pull ubuntu:16.04`

    * 从指定仓库注册服务器下载

        `$ sudo docker pull xxx.xxx.xxx:5000/ubuntu16.04`

* 运行镜像

    `sudo docker run -t -i ubuntu:16.04 /bin/bash`

    进入一个命令行，则表示成功


## 镜像操作

### 查看本地镜像

```
$ sudo docker imags
REPOSITORY TAG IMAGE ID CREATED SIZE
ubuntu xfce 671914997294 5 hours ago 1.8GB
ubuntu v5 250a07604d80 6 hours ago 1GB
ubuntu v4 e96529597a49 6 hours ago 960MB
ubuntu v3 4c5935501824 7 hours ago 938MB
ubuntu v2 6507796d463e 8 hours ago 894MB
ubuntu v1 6b5b17d9ebc5 23 hours ago 458MB
172.26.11.112:5000/ubuntu-16.04_basic latest 9abac4bff8fe 23 hours ago 332MB
ubuntu V0 9abac4bff8fe 23 hours ago 332MB
ubuntu 16.04 f7b3f317ec73 2 weeks ago 117MB
registry latest 136c8b16df20 5 weeks ago 33.2MB
```

**字段含义如下：**

* repository: 来自那个仓库
* TAG: 镜像的标记
* IMGAE ID: 镜像的唯一ID号
* CREATED: 创建时间
* SIZE: 镜像大小

### 修改镜像

* **进入容器修改**

    ```
    $ sudo docker run -t -i ubuntu:16.04 /bin/bash
    root@ef762a185a4a:/#
    root@ef762a185a4a:/#apt install gcc-4.8
    root@ef762a185a4a:/#exit

    $ sudo docker commit -m "Add gcc." -a "quincy" ef762a185a4a ubuntu:v1
    注意： -m: 说明信息， -a 用户信息， 后面接着的时容器ID号（root@的后面）， ubuntu:v1 分别代表仓库名和标记
    ```

* **使用Dockfile**
    新建一个目录和一个文件命名为Dockerfile

    ```
    $ mkdir test
    $ cd test
    $ vim Dockerfile
    ```

    写入如下内容：

    ```
    # This is a comment
    FROM ubuntu:16.04
    MAINTAINER quincy <quincy@xxx.com>
    RUN apt-get update
    RUN apt-get install gcc
    RUN apt-get install g++
    ```

    **语法说明：**

    ```
    # 注释
    FROM 仓库名：标记
    MAINTAINER 用户 邮箱
    RUN 命令
    ```

    创建：\(注意后面一个点，表示当前路径，也可以指定为一个包含Dockerfile文件的路径\)
    
    `$ sudo docker build -t="ubuntu:v2" .`

* **修改镜像标记**

    `$ sudo docker tag 镜像ID 仓库：标签`

### 上传镜像

* **安装 registry**
  - 从 docker-hub 安装:

    `$ sudo docker run -d -p 5000:5000 registry`
    
    默认情况下，仓库会被创建在容器的 /tmp/registry目录下， 可以通过 -v 参数将镜像文件存放在本地
    
    如 `sudo docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry`

  - 从源安装:
    
    ```
    $ sudo apt-get install -y build-essential python-dev libevent-dev python-pip liblzma-dev
    $ sudo pip install docker-registry
    $ sudo docker run -d -p 5000:5000 registry
    ```

* **修改镜像tag**

    命令格式为：`docker tag IMAGE:TAG REGISTRYHOST/NAME`
    
    如：`$ sudo docker tag ubuntu:v1 192.168.10.11/ubuntu-test`

* **用curl 查看仓库**

    `$curl http://192.168.10.11/v1/search`

* **下载镜像**

    `$sudo docker pull 192.168.1.11:5000/ubuntu-test`

### 删除镜像

    `$ sudo docker rmi 选项 镜像`


### 导出与导入镜像
- **导出镜像**

    命令格式： `docker save IMAGE(镜像)`

    如：`docker save 9610cfc68e8d > /home/myubuntu-save-1204.tar `
    
- **导入镜像**

    命令格式：`docker load IMAGE`

    如：`docker load < /home/myubuntu-save-1204.tar`

## 挂载目录

    如果需要容器访问主机的一些文件或目录，可以使用 `-v` 选项将文件或目录挂载到容器中

    如`sudo docker run -ti -v /home/hqx:/home/hqx ubuntu:16.04 /bin/bash`

## 容器操作

* **查看所有容器**
    
    `$ sudo docker ps -a`

* **删除容器**
    
    `$ sudo docker rm 容器名或ID`

* **启动，停止或重启**

    `$ sudo docker start/stop/restart 容器名或容器ID`

## 填坑记录

1. 容器内安装gdb,无法执行 gdb 或 gdb 结果为空

    gdb 需要访问程序运行时空间，需要root权限，使用 `--privileged` 权限

2. 运行有界面程序

    ```
    $ XSOCK=/tmp/.X11-unix/X0
    $ xhost +
    $ sudo docker run -ti -v $XSOCK:$XSOCK  -e "DISPLAY=unix:0.0" ubuntu:v4 /bin/bash
    root@dasfdlfja: firefox
    ```

3. 安装界面

    推荐xfce vnc

5. HTTP error

    在自己搭建的私有仓库上，推送镜像或下载镜像时，经常碰到下述错误：

    ```
    Get https://172.26.11.112:5000/v1/_ping: http: server gave HTTP response to HTTPS client
    http: server gave HTTP response to HTTPS client
    ```

    解决方案如下：

    ```
    $ sduo vim /etc/docker/daemon.json
    写入如下内容，ip地址填为私有仓库的地址
    { "insecure-registries":["10.10.239.222:5000"] }

    $ sudo service docker restart
    ```

4. 其他参考资料

    [https://yeasy.gitbooks.io/docker\_practice/content/](https://yeasy.gitbooks.io/docker_practice/content/)
