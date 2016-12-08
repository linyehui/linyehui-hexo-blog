---
title: 'Windows App Certification Kit 一启动就Crash'
layout: post
tags:
    - appcert
    - certification
    - winrt
---

# 问题
Windows 8 Consumer Preview下，用Windows App Certification Kit认证自己的应用，认证到一半卡住了，10分钟了都没动静，连系统都卡死了，于是我长按“Power”键进行了暴力重启，重启之后就悲剧了：Windows App Certification Kit 一启动就Crash！！！

# 方案一，重装SDK
在微软官网下载了包含Windows App Certification   Kit的SDK进行安装，但是发现根本没用，难不成我只能重装系统吗？！！

# 方案二，删除配置文件

好在我有Everything，换了两个关键字后，我找到了他的配置文件所在  

## 我用的关键字：
1. App Certification Kit
2. AppCertKit

## 找到的配置路径：
C:\Users\XXXX\AppData\Local\Microsoft\AppCertKit

## 把AppCertKit路径下的文件全部删了，就可以解决问题。