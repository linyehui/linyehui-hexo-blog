---
title: '跨平台代码缩进'
layout: post
tags:
    - cross-platform
    - indent-style
    - android
    - ios
    - windows
---

# 需求
跨平台的代码，在各个平台下的代码显示都能正确对齐，中文不乱码

# 规范内容

## 文件名
> 文件名全小写加下划线

## 文件编码
> 文件编码使用 UTF-8 With BOM

Eclipse,XCode 5.1+,VS2012都已经支持UTF-8 With BOM这个文件编码格式，中文不乱码

## TAB缩进
> 1.使用等宽字体  

> 2.使用空格替代TAB，一个TAB对应4个空格  

> .  

> PS. 主流IDE下都有选项可以将TAB自动转换为空格  


### 使用空格替代TAB，在三大主流IDE下如何配置

##### Visual Studio 2012
![Visual Studio](/media/files/2014/08/29/vs.png)
##### ADT(Eclipse)
![Eclipse](/media/files/2014/08/29/eclipse.png)
##### Xcode 5.1.1
![Xcode](/media/files/2014/08/29/xcode.jpg)