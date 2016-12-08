---
title: 'New or updated in Windows 8.1 Preview'
layout: post
tags:
    - win8
    - winrt
---

上周的Build 2013上微软发布了Windows 8.1 Preview，微软对外公布了8.1的更新信息：  

[New or updated in Windows 8.1 Preview](http://msdn.microsoft.com/zh-cn/library/windows/apps/bg182889.aspx)

我自己看了之后比较感兴趣的点有下面这些：

# 1. General updates

### App bundle support

资源包，方便做多语言的支持，比现在散落在目录下规范很多

# 2. Diagnostics

### Mixed-language debugging

终于可以同时调试Javascript和C++了，只能说现在的版本太搓了 

### Managed app debugging

动态修改变量的值，实用功能

### Asynchronous debugging improvements

task任务的堆栈可以追溯到创建的地方，这个调试异步任务时非常实用

### DOM Explorer

得到了加强

### JavaScript Console

不用自己console.dir了

### JavaScript Memory Profiler

现在的发现点还不够智能，需要验证下好不好用才知道

### JavaScript UI Responsiveness

实用

# 3. JavaScript editor

小改动都比较实用

# 4. C++ development

### C++ REST SDK

我们自己用不到，但是这也算是当下必备的基础库了

### IDE productivity features

优化了编译速度，这个比较期待

### Build-time performance enhancements

优化了并行编译，期待

# 5. HTML design tools

使用的优化，对开发影响不大

# 6. XAML design tools

使用的优化，对开发影响不大

# 7. Windows App Certification Kit 3.0

连这个小工具都做了并行优化，挺用心的嘛

### New validation tests

##### File extensions and protocols

数量在规则上并没有什么变化，只是加了检查，网盘类应用应该比较关心这个

##### JavaScript background tasks

考虑省电的缘故，后台不给常驻了，但如果应用配合服务端的话，“常驻”比较难被检查出来吧？不知道微软是怎么检查的

### Test reports

之前遇到过一个莫名其妙的启动性能Failed，给的report detail根本就看不出是什么原因导致的，希望这个版本能够有所改进。

# 8. 开发建议

从之前微软发布Windows 8 预览版的节操来看，Preview版本和RTM版本还是会有比较大的差异，所以开发者不要着急着全面跟进8.1的新特性；
先把这些特性都研究下，改改兼容性的Bug，能做到8.1正式发布后第一时间有新版本跟进也就可以了。

Good luck， have fun :)