---
title: 'cocos2d-js调试Spidermonkey代码'
layout: post
tags:
    - cocos2d-js
    - spidermonkey
    - c++
---

## 需求
cocos2d-js-3.1中的spidermonkey是直接以头文件+.a文件的方式整合的，但是有些代码会crash在spidermonkey的代码中，所以我需要调试spidermonkey的C++代码。

## 解决

### 寻找cocos2d-js-3.1使用的spidermonkey版本

#### 尝试1，v23版本
版本 v23，看当前使用的版本，一开始的想法是查看本地cocos2d-x目录下的CHANGELOG，我看到的最新的是：  


```
cocos2d-x-3.0alpha0 @Sep.19 2013
Upgraded SpiderMonkey to Firefox v23
```

而spidermonker目录下的README告诉我们需要去这个github仓库取代码  


```
.\frameworks\js-bindings\external\spidermonkey\README.md
https://github.com/ricardoquesada/Spidermonkey
```

找了下v23的log，发下没有release，所以只能从特定的commit下载zip包下来

https://github.com/ricardoquesada/Spidermonkey/tree/c8f78c30b633025f48fc0075e5f9a706639c7ac4

##### 结论：编译后发现版本不对，头文件不对

#### 尝试2，v28版本
本地的CHANGELOG不对，那么就看下在线版本的；  

我们的cocos2d-js版本是3.1对应到在线的CHANGELOG，其中的Spidermonkey版本是v28  

https://github.com/cocos2d/cocos2d-js/blob/develop/CHANGELOG

同样的还是没有release版本，所以还是到对应的commit页面下载  

https://github.com/ricardoquesada/Spidermonkey/tree/90747bc0ccb8e9fa1d56ef7e9c1b8b7b1bee4c96

```
Cocos2d-JS-v3.0 beta @ May.23, 2014
[JSB]Upgraded SpiderMonkey to v28.

Cocos2d-JS-v3.0 alpha2 @ April.14, 2014
[JSB]Update precompiled SpiderMonkey to support iOS 64 bit devices.


ricardoquesada authored on 25 Apr 2014
Merge pull request #28 from pandamicro/v28 …
[v28] Upgrade to Spidermonkey v28
```

##### 编译
进入js/src/build-android，执行./build.sh -r
对比生成的apidermonkey-android中的include文件和cocos2d-js目录下的文件，一致，看来应该是这个版本了

### 结论
cocos2d-js-3.1 使用的spidermonkey版本是v28，可以从这里下载到:
https://github.com/ricardoquesada/Spidermonkey/tree/90747bc0ccb8e9fa1d56ef7e9c1b8b7b1bee4c96

进入js/src/build-android目录下执行./build.sh -r后将生成的三个目录覆盖到工程目录就可以调试了。