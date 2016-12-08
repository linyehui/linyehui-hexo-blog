---
title: 'How To Load Win32 Dll With Metro App'
layout: post
tags:
    - win8
    - winrt
---

WinRT下LoadLibrary已经不能用了，那么应该怎么用呢？来自MSDN Forum的答案告诉我们：  

1、使用LoadPackagedLibrary/GetProcAddress  

2、将dll添加到Metro App工程里面，发布时一起打包（这一步也是必须的，dll放在相同目录是不行的，会出现错误：Specifed module not found）  


**参考：**  

[How to load win32 dll with C# on metro app](http://social.msdn.microsoft.com/Forums/en-US/winappswithcsharp/thread/1bec1fd8-3f1c-4f84-a2b9-f3e9086dce39)