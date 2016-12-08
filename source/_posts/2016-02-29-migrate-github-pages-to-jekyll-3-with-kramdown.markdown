---
title: '将 GitHub Pages 从 rdiscount 切换到 kramdown'
layout: post
tags:
    - github
    - markdown
    - jekyll
---

## 需求

需求来自Github的一条公告:

[GitHub Pages now faster and simpler with Jekyll 3.0](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0)

```
Starting May 1st, 2016, GitHub Pages will only support kramdown, Jekyll's default Markdown engine.
```


GitHub宣布从 2016 年 5 月 1 日起，GitHub Pages 将只支持 kramdown 作为唯一的 Markdown 引擎，而我之前使用的rdiscount需要做迁移。

## 迁移

### 升级本地Jekyll到3.0方便调试
```
$ gem install jekyll
```

### _config.yml

#### 老的配置文件
```json
markdown: rdiscount
highlighter: pygments
permalink: /:year/:month/:day/:title.html
name: "HABIT IS POWER"

author: "linyehui"
atom-baseurl: "http://linyehui.github.io"
disqus: "linyehui"

navigations:
```

#### 更新后的配置文件

```json
markdown: kramdown
kramdown:
    input: GFM
highlighter: rouge
permalink: /:year/:month/:day/:title.html
name: "HABIT IS POWER"

author: "linyehui"
atom-baseurl: "http://linyehui.github.io"
disqus: "linyehui"

navigations:
```


## 升级后的格式问题

升级之后，最大的问题就是换行问题，再有就是代码格式问题

### 换行问题
这个麻烦一点，找了下，没办法从配置上修改，只好用正则表达式全局搜索替换

#### 连续两行#开头的话中间加一个空行
```
(#.+\s)(^#)
$1\n$2
```


#### 行末尾两个空格替换成一个空行
```
(  $)
$1\n
```


### 代码格式

我涉及到的代码格式主要是这几种，代码块的左```之后跟的字符串写法换一下就好了

```
object-c
objc

C++
cpp

c#
csharp

```


