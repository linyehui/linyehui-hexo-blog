---
title: '我想和这个世界谈谈：Google Static Maps API'
layout: post
tags:
    - google
    - map
    - zaina
---

当您看到这封信的时候，我们刚刚作出了一个非常艰难的决定。我要跟大家谈谈Google Static Maps API那点事。

Google Static Maps API V2 开发人员指南  

<http://code.google.com/intl/zh-CN/apis/maps/documentation/staticmaps/>  


这个API非常好用，因为一个URL就对应着一个图片，这样的API不仅可以在Web程序中使用个，也可以很方便的在App中使用。  

BaiduMap也提供了类似的API，但只有JS的调用，没有见到直接的URL使用方式，这样的话就不好在客户端程序中使用。

这套API其实也很简单，大部分东西都在开发指南中提到了，这里我只介绍下我在做【在哪】时遇到的一个开发指南中没有提到的东西：  

地图移动的偏移量

如果地图的Level不变的话，每次移动的偏移量其实是很容易试出来的，但地图在放大和缩小之后难道每个缩放等级都要去试出来吗？  

从0-21都试一次的话还是比较麻烦。

我自己试了下，发现了规律：2倍。  

是的，放大之后的偏移量是之前的2倍，缩小之后的偏移量是之前的1/2，实验之后基本差不多。