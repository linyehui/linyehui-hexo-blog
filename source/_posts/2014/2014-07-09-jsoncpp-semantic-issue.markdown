---
title: 'jsoncpp在Xcode下编译出现semantic issue'
layout: post
tags:
    - jsoncpp
    - ios
    - xcode
---

# 问题
Xcode 5.0.2下编译Cocos2d-x工程，工程中使用了jsoncpp-0.5.0，编译的时候出现这个问题，找了半天不知道怎么解决：
> semantic issue
Variable has incomplete type 'Json::StyledWriter'

# 解决方案
升级jsoncpp-0.6.0后解决，我用的是这个版本:[github jsoncpp](https://github.com/rboulton/jsoncpp)
