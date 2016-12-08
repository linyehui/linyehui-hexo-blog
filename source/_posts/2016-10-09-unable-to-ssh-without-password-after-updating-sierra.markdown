---
title: 'Mac升级到Sierra后SSH自动登录提示输入密码'
layout: post
tags:
    - mac
    - sierra
    - ssh
---

## 需求
把Mac升级到了Sierra，发现我的git无法ssh登录了，一开始还以为是服务器的问题，因为问题的现象很像：
[发现 Gitlab 的一个权限问题](http://i.am.simonkuang.com/2015/12/05/%E5%8F%91%E7%8E%B0-Gitlab-%E7%9A%84%E4%B8%80%E4%B8%AA%E6%9D%83%E9%99%90%E9%97%AE%E9%A2%98/)中描述的那样，要求我输入密码；

不过后面突然想到今天升级了系统，所以加上了Sierra关键字又搜索了下，发现了同类中人：  

[Unable to ssh without password after installing Sierra](https://forums.developer.apple.com/thread/48794)

[苹果系统升级到sierra后，ssh远程证书免密码登录出错了](https://www.oschina.net/question/1262012_2199479)


## 解决方案
上面两个帖子里的办法没有能解决我的问题，我最终的解决方案是：

> 在Sierra系统下重新生成id_rsa和id_rsa.pub，重新设置服务端的id_rsa.pub证书

### 一个小问题
一开始我生成的证书没有命名为默认的名字：id_rsa，会出现这个错误：
[github提示Permission denied (publickey)，如何才能解决？](https://www.zhihu.com/question/21402411)

#### 这个问题其实好解决，两个方案：

* 方案一，把老的id_ras和id_rsa.pub备份起来，把新的证书改成默认名字
* 方案二，[adding SSH key to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
