---
title: 'setStatusBarOrientation:animated: not working'
layout: post
tags:
    - ios
    - orientation
---

## 问题
有个视频播放页面需要手动控制页面的横竖屏状态，使用setStatusBarOrientation可以达到这个效果；
但是很奇怪的事情发生了，我们发现代码在某台机器的某种情况下会出现setStatusBarOrientation调用无效。

## 原因
直接的原因是我们使用了BugTags，并且在白名单用户登录后打开了BugTags的悬浮小球，悬浮小球出现的时候setStatusBarOrientation调用就会无效；

分析后发现真正的原因是，页面已经不是the top-most full-screen view controller了：
> The setStatusBarOrientation:animated: method is not deprecated outright. It now works only if the supportedInterfaceOrientations method of the top-most full-screen view controller returns 0

## 解决方案
知道原因就简单了，需要全屏的时候隐藏BugTags的悬浮球即可
> BTGInvocationEventNone

## 参考
[setStatusBarOrientation:animated: not working in iOS 6](http://stackoverflow.com/questions/12563954/setstatusbarorientationanimated-not-working-in-ios-6)
[setStatusBarOrientation 未生效的解决办法](http://blog.csdn.net/ginhoor/article/details/20454229)