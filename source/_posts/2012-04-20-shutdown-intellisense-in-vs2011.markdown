---
title: 'VS2011中关掉坑爹的IntelliSense'
layout: post
tags:
    - intellisense
    - vs2011
---

不是一般的坑爹，工程稍微大一点，性能就差得像一坨屎！  

好在VS2011后关起来方便了很多，因为一开始就做Win8，而Win8的内部版本又经常变，每次重装之后我都要遭受下IntelliSense的折磨，我甚至不记得自己是否成绩写过类似的文案来记录如何去掉这个恶心的东西。

# IntelliSense关闭方法：
```
Tools->OPtions->Text Editor->C/C++->Advanced->IntelliSense
Disable IntelliSense 改成True
```

其他语言的关闭方法类似