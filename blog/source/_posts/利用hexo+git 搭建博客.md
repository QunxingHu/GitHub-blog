---
title: 利用hexo + git 搭建博客
date: 2016-08-15 23:46:36
categories:
	- 术业专攻
tags: 
    - hexo
    - git
---
这篇文章主要记录本人利用hexo搭建github博客的步骤，对于搭建后的博格主题的美化，在主题的readme都有详细阐述，这里不再赘述。另外，附加文章常见的格式。

<!-- more -->
### 搭建博客步骤
#### 环境配置
1. 下载安装 [git][1]
> - 添加本地SSH key至github
> - git config -- global user.name "xxx"
> - git config -- global user.email "xxx"
> - 在github上添加新分支为名为 username.githup.io

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


## hexo文章模板
```markdown
---
title: 文章名
date: 时间
categories:
	- 文章分类1
	- 文章分类2
tags: 
    - 文章标签1
    - 文章标签2
top: 1 		#文章置顶
---

这里是显示在主页中的文章摘要。
<!-- more -->
这是文章正文（主页显示readmore 按钮，点击后可见正文）
```
文章分类，与文章标签分类，需主题支持。本人所用的[yelee][4]。


## 添加文章置顶功能
修改`node_modules/hexo-generator-index/lib/generator.js`内容如下：
```javascript?linenums
'use strict';

var pagination = require('hexo-pagination');

module.exports = function(locals){
  var config = this.config;
  var posts = locals.posts;

    posts.data = posts.data.sort(function(a, b) {
        if(a.top && b.top) { // 两篇文章top都有定义
            if(a.top == b.top) return b.date - a.date; // 若top值一样则按照文章日期降序排
            else return b.top - a.top; // 否则按照top值降序排
        }
        else if(a.top && !b.top) { // 以下是只有一篇文章top有定义，那么将有top的排在前面（这里用异或操作居然不行233）
            return -1;
        }
        else if(!a.top && b.top) {
            return 1;
        }
        else return b.date - a.date; // 都没定义按照文章日期降序排

    });

  var paginationDir = config.pagination_dir || 'page';

  return pagination('', posts, {
    perPage: config.index_generator.per_page,
    layout: ['index', 'archive'],
    format: paginationDir + '/%d/',
    data: {
      __index: true
    }
  });
};
```

如有疑问和建议，欢迎指导和提问。

  [1]: https://git-scm.com/downloads/
  [2]: https://nodejs.org/en/
  [3]: https://hexo.io/zh-cn/
  [4]: https://github.com/MOxFIVE/hexo-theme-yelee