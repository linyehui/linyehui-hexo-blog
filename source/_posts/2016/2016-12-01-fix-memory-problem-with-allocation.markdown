---
title: '使用Instruments Allocations排查内存释放不及时的问题'
layout: post
tags:
    - mac
    - ios
    - memory-leak
    - instruments
---

## 问题描述
使用ARC的过程中，由于循环引用或者其他使用不当，会造成对象的内存没有及时释放，而这些内存在页面关闭的时候又会随着页面的生命周期一起释放，这就给我们排查“内存泄漏”带来了麻烦；

这种内存问题用Leaks是找不出问题的，因为这些内存其实只是释放不及时，而不是没有释放。

## 解决方案
使用Instruments Allocations的Mark Generation功能能帮助我们排查出这种问题。

### 使用之前先说几个Instruments的调试技巧

1、调试内存可以用Debug模式来调试，但是记得在工程设置中把Debug模式的Debug Information Format设置成：DWARF with dSYM File，否则无法在Instruments中看到出问题的符号对应的代码

2、性能调试真机调试的时候有时候连接不上，试试重启手机，实在还是不行，可以换成Debug模式，相对没那么容易连接不上手机(修改方法：Edit Scheme->Profile->Build Configuration 修改成Debug)

### 一个典型案例，问题描述
假定我们有一个页面MainViewController，打开这个页面后会开始进行视频直播，在直播的过程中会出现内存没有及时释放，比如10分钟后应用的内存占用已经达到了200MB，但是页面一关闭内存有都正确释放了。

针对这样的一个问题我们应该怎么来排查呢？

### 排查步骤
1、Xcode中启动Profile工具
![image](/media/files/2016/12/01/01.png)

2、选择Allocations工具，点击红色按钮开始“录制”，这时Instruments会启动我们的应用
![image](/media/files/2016/12/01/02.png)

3、打开有内存释放问题的页面，稍微等一下，等页面的常规初始化完成，比如等个10秒钟

4、从Instruments右下角的区域切换到“Display Settings” Tab页，并点击“Mark Generation”生成第一个Generation：Generation A；
这个相当于是我们判断内存没有及时释放的起始点
![image](/media/files/2016/12/01/04.png)

5、为了让没有及时释放的内存泄漏问题，累积的更多一点，第二次Mark的时间可以稍微多等久一点（等多久这个取决于你的内存问题出现所需要的时间，比如2分钟），然后再次点击“Mark Generation”，这个时候你就会发现两次Generation之间的内存增长，而这里面的增长其实就是我们所寻找的没有及时释放的内存：

![image](/media/files/2016/12/01/05.png)

6、点击小箭头查看更详细的信息，根据内存占用的比例，找到占用比例最高的，并且不断展开，直到找到我们自己的代码，这就是出问题的地方了。
![image](/media/files/2016/12/01/06.png)


