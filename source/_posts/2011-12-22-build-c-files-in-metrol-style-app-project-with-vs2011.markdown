---
title: 'VS2011 Preview版本下在Metrol style app中使用.C文件'
layout: post
tags:
    - microsoft-bug
    - win8
    - winrt
---

# 问题描述：
最近我在将一些代码从Win32迁移到WinRT，开发环境是Win8的Windows Developer Preview版和Visual Studio 11 Developer Preview，在迁移sqlite和zlib时发现了一个诡异的问题：  

编译.C后缀的文件，无论是什么文件，只要以c类型编译都会出现一个Error


> E:\SQliteBuildDemo\SQliteBuildDemo\sqlite3.c : fatal error C1083: Cannot open compiler intermediate file: 
> 'C:\Users\linyehui\AppData\Local\Temp\_CL_01c0d5cfmd': No such file or directory


但如果什么都不改，只是把工程中的.c文件属性里的编译类型改成C++，就不会有这个错误，但需要将C的语法转换成C++语法才可以。

# 问题解决方案：

## 1、不靠谱的第一种方案
既然用C++编译可以，那就改成C++编译，然后把C的语法改成C++。

### 弊端：
这个工作量和.C文件本身的代码风格会有很大的差异，像我们的工程用到了sqlite和zlib，sqlite代码相对少点，但是把所有zlib中的.c改造成c++那真是一项繁重的体力活!!

## 2、方案2：Win32 Static Lib
新建了一个3rdparty的Win32 static lib工程，把sqlite和zlib的代码都添加进去，然后在Metro style app工程中include上这个3rdparty.lib

### 弊端或者说是风险：
在Metrol style app中引用win32的代码要特别留意，因为WinRT下只包含了Win32 API的一个子集，所以如果你在Win32工程下使用了WinRT不支持的API，那么工程虽然编译过了，但在WinRT下的使用其实是会有问题的，到时你可能会被这种错误搞的莫名其妙。

举例来说：  

WinRT下是不支持WinSock2.h这套API的，但如果你的Win32 static lib工程中使用了这样的方法，而Metrol style app工程又使用了这个static lib，那么你的应用其实是有问题的，甚至有可能都无法通过Windows Store的上架申请。


# 小结：
两个方案我都尝试过，都是可行的，但都有弊端，其实这个问题归根结底就是Visual Studio 11 Developer Preview的一个bug，下一个VS2011版本应该就会修正了。  


嗯，这是微软的一个BUG。