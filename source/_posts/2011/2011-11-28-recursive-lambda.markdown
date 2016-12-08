---
title: 'Lambda表达式的递归'
layout: post
tags:
    - lambda
    - winrt
---

WinRT下异步操作很多，所以Lambda也很多，如果要让Lambda部分的函数递归如何实现？  

这个需求其实在C++下会比较突出一点，因为C#和VB下是managed的代码，比较容易实现回调事件。

其实也是一个小的“奇技淫巧”，不见得是什么好东西：

缓存下这个Lambda表达式的地址：opRead->Completed = (Type sender){Lambda};  

在op->Start();之前缓存下这个地址：m_handler = opRead->Completed;  

然后你就可以在Lambda内部继续使用这个地址，于是乎递归达成。