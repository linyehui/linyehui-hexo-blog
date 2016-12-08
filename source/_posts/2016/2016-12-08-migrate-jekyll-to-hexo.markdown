---
title: '把GitHub Pages上的博客从Jekyll迁移到Hexo'
date: 2016-12-08 17:21:02
tags:
    - hexo
    - jekyll
    - gitpages
---

## 前言
之前用的Jekyll，最大的痛点是对Markdown的代码格式支持的不是很好，尝试过一些方法，不过都不太理想；
最近偶然看到Hexo的介绍，决定尝试一下；
迁移过程中有些小Tips觉得有必要小结下，对于迁移的人来说还是很有用的。

* 迁移结果：
最终效果：http://linyehui.me
Hexo生成的代码：https://github.com/linyehui/linyehui.github.io
Blog源代码：https://github.com/linyehui/linyehui-hexo-blog

## 迁移步骤
* 我的系统是Mac OS 10.12.1
* 我原来的Jekylly代码在这个仓库下：https://github.com/linyehui/linyehui.github.io
* 代码备份
	- 从master建立分支用于代码备份（多留个心眼，后面发现很有用），备份用的分支：[release-jekyll](https://github.com/linyehui/linyehui.github.io/tree/release-jekyll)
	- 保险起见，我把本地的github 仓库也备份了下

* 创建一个新的github仓库用户存储我的hexo blog代码：https://github.com/linyehui/linyehui-hexo-blog，git clone到本地，得到hexo的本地根目录：linyehui-hexo-blog
* 初始化hexo本地环境
```shell
git clone https://github.com/linyehui/linyehui-hexo-blog
cd linyehui-hexo-blog
npm install -g hexo-cli

hexo init

npm install hexo --save
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save
```
	这一步完成的时候，你就可以在根目录下执行hexo server，并且可以在浏览器上看到Hello World页面了

* 配置下_config.yml，这里就是各种名字的配置，配置完了之后，浏览器预览没问题就行了

* 配置theme，我用的是iissnan/hexo-theme-next
```
# git clone到另外一个目录，然后复制到./linyehui-hexo-blog/themes/next目录下
git clone https://github.com/iissnan/hexo-theme-next

# 复制hexo-theme-next目录到./linyehui-hexo-blog/themes/next

# 修改_config.yml中的这行文件，主题名字和目录名一致
theme: next
```
	根据自己的需要修改下主题的配置：./linyehui-hexo-blog/themes/next/_cofig.yml

* 迁移之前博客的文章
	- 把Jekyll版本的_posts目录下的文件复制到./linyehui-hexo-blog/source/_posts/
	- 把之前根目录下的媒体文件目录：media（图片）复制到./linyehui-hexo-blog/source/media
	
	浏览器中预览下，首页应该能看到你的文章了，数据迁移非常简单，就这一步

* 创建tags文件
```
$ cd linyehui-hexo-blog
$ hexo new page tags

# 文件内容如下：
title: 标签
date: 2016-12-08 12:36:06
type: "tags"
comments: false
---
```

* 上面都没问题，就可以发布了
```
hexo deploy
```
	每次发布，你都会发现你的github仓库的master分支只有两次commit，也就是说hexo deploy把老的master分支给删除了……

* 发布没问题，我就把新的代码仓库下的文件给入库了，记得配置下.gitignore
```
.DS_Store
.deploy_git
db.json
node_modules
public
```

* 到这一步blog的主体功能已经迁移完成，剩下的就是些美化、图标、头像，这里就不细说了

## 迁移Tips
* hexo deploy 每次会将master分支删除后重新创建，迁移之前的代码切记要建立分支进行备份
* hexo deploy 所做的事情：删除现有的master分支，并建立新的master分支，把hexo目录下的.deploy_git目录push到master分支上
* source/_posts目录支持按年份划分目录，_config.yml中这么配置就行了：
```yaml
new_post_name: :year/:year-:month-:day-:title.md # File name of new posts
```

## 参考文章
[Jekyll迁移到Hexo搭建个人博客](http://www.ezlippi.com/blog/2016/02/jekyll-to-hexo.html)
[将博客从Jekyll迁移至Hexo](http://www.wukai.me/2016/01/13/blog-jekyll-to-hexo/)

