---
title: '第三方库在WinRT下的使用'
layout: post
tags:
    - 3rdparty
    - port
    - sqlite
    - win8
    - winrt
    - zlib
---

> 本文发表时Win8和VS2011还没正式发布，处于Developer Preview版本，之后的情况可能不是这样了

很多第三方库类似zlib,sqlite都是.c实现的，这些库如何用到WinRT中呢？  


# 推荐的方法：
* 1、在VS2011下新建一个Win32 Static lib工程，比如，我自己就是新建了一个叫做“3rdparty”的工程，然后把所有的第三方库添加进去，编译成一个lib，直接在Metro style apps中使用，用起来和Win32下一摸一样
* 2、**这个步骤是必须的：**[http://msdn.microsoft.com/en-us/library/windows/apps/br230262(v=VS.85).aspx Validating your package on your local machine]，你的应用必须通过了"Windows App Certification Kit"里的“Validate Metro style App”，你的第三方库使用才算是正常的

# 不推荐的方法
把.c文件移植成.cpp

# 可能存在的疑问：
* 1、VS2011不能新建Win32工程
Answer：  

你用的是系统自带的Express版本，这个是没有的，要用完整版，从这里可以下载到：  

[Microsoft® Visual Studio® 11 Developer Preview (ISO)](http://www.microsoft.com/download/en/details.aspx?id=27538)

* 2、认证不通过
Answer:  

照着指引改吧，第三方库写的不好的话，改起来就会很痛苦；或者你等待Win8普及后第三方库作者更新代码。