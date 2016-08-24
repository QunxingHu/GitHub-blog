---
title: 利用hexo + git 搭建博客
categories:
	- 术业专攻
tags: 
    - hexo
    - git
---
### 搭建博客步骤
#### 环境配置
1. 下载安装 [git][1]
> - 添加本地SSH key至github
> - git config -- global user.name "xxx"
> - git config -- global user.email "xxx"
> - 在github上添加新分支为名为 username.githup.io
<!-- more -->
**说明：**
> 已经添加SSHkey 的不必重复添加，git config -global选项可以不用（可以单独为某个文件夹指定配置，”xxx“,username替换为自己的用户名和邮箱）

#### 2. 下载安装 [nodejs][2]
#### 3. 下载安装 [Hexo][3]
> 在命令行输入npm install hexo-cli -g

#### 创建博客
1.  新建文件夹 githublog/blog (新建两层目录是为了将blog文件更方便的保存到git )
2. hexo init blog
3. cd blog
4. npm install
3. hexo generate (可简写为hexo g)
4. hexo server (启动本地服务，进入 http://localhost:4000 预览)
5. 修改 blog 目录下的配置文件 `_config.yml`
> 在文件末尾处修改如下：
``` xml
deploy:
     type: git
     repo: https://github.com/qunxinghu/qunxinghu.github.io.git
     branch: master
```
说明：repo 地址为在github上新建的分支地址，修改为自己的分支地址。
6.  执行命令 `npm install hexo-deployer-git --save`
7. hexo deploy 
8.  访问 http://qunxinghu.github.io/ 进入博客（http://你的git用户名.github.io/）


#### 博客维护
1. 博客美化，hexo官网提供了大量主题，下载到本地后，修改blog目录下的`_config.yml`配置文件中的theme选项即可。
2. 新建文章   hexo new "postName" 
3. 部署步骤每次部署的步骤，可按以下三步来进行。   
``` powershell?linenums
hexo clean   
hexo generate 
hexo deploy
```
4. 多地编辑博客
在 githubblog 目录中建立 git, 将其 push 到 github 中，在其他电脑上配置好本地环境后（git， nodejs， hexo）,将 githubblog clone 到本地即可进行博客的操作。

#### 一些常用命令：
``` powershell
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub
hexo help # 查看帮助
hexo version #查看Hexo的版本
```
如有疑问和建议，欢迎指导和提问。


  [1]: https://git-scm.com/downloads/
  [2]: https://nodejs.org/en/
  [3]: https://hexo.io/zh-cn/