---
title: 'char的定义在iOS和Android下是不同的'
layout: post
tags:
    - android
    - c++
    - cross-platform
    - ios
---

**char is different in iOS and Android!**
**跨平台开发时很容易忽略的非常坑爹的一个区别。**

------

我的需求是实现一个算法，这个算法在iOS和Android下需要保持一致的结果，很自然的我用C++实现了一份跨平台(cross-platform)代码，在两个平台都可以用，但这份代码在iOS和Android下竟然出现了不同的结果，排查了很久后发现了这个不同：

> iOS下char is signed，Android下char is unsigned
也就是说，在iOS下这两个定义是等同的：
> char cNum = 100;
> signed char cNum = 100;

而在Android下这两个定义是等同的：
> char cNum = 100;
> unsigned char cNum = 100;

当你用char进行运算的时候，signed和unsigned是有正负号差异的，会导致你的结果变得莫名其妙。

这个代码在iOS和Android下代码执行结果是不一样的
> char cNum = 0x80;
> float fResult = cNum + 80;

**解决方案：**
声明char类型的变量时，明确写上是signed char还是unsigned char。

发现了问题之后，再反过来找资料的时候发现了这个，不得不感慨：问题总在解决之后突然就变得简单了：
[C Compiler differences for iOS and Android development](http://stackoverflow.com/questions/21467195/c-compiler-differences-for-ios-and-android-development)

> In your Android C implementation, char is unsigned, and the conversion from -7 in double to char is producing zero. (The behavior of this conversion when the value cannot be represented in the destination type is not defined by the C standard.)
> 
> In iOS, char is signed, and the conversion of -7 in double to char produces -7.

PS.
特别要说明下我的开发工具版本：
XCode:5.0.2
官方ADT：22.3.0