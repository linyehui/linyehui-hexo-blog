---
title: 'WinRT for Win32 programmers, Part I-Hello WinRT'
layout: post
tags:
    - winrt
    - winrtforwin32programmers
---

> 本文首发于 <http://www.cnblogs.com/linyehui/>   这里是我的Blog备份。

WinRT, Windows Runtime 是微软的下一代API体系，在Build大会上为人们所熟知。
如果说Windows 8是微软的未来的话，那么WinRT则是Win32的未来，对于千千万万的Win32程序员们来说，如果还想继续在Windows平台下发展，那么了解、学习并熟悉WinRT就是你居家旅行的不二之选。 

# 目录：
* 一、什么是WinRT  

* 二、开发环境  

* 三、Hello WinRT  


# 一、什么是WinRT
说起WinRT，我们必须先看下这样一张图片：  

![2012033015101194.png](http://pic002.cnblogs.com/images/2012/347098/2012033015101194.png)
 这种图片来源于2011年9月13号在美国洛杉矶举办的Build 2011，从图上我们可以发现：  

1. Windows 8 整个系统分成了Metro style和Desktop两个体系，而WinRT则是全新的Metro应用程序架构的基础所在；
2. WinRT具备了多语言的支持能力，不仅支持C/C++，还支持.NET的主力语言C#和VB，同时还支持HTML下最普及的JavaScript；
3. WinRT和Win32、.NET是相互独立的API体系；

### 提示
实际上WinRT还支持部分Win32和.NET的API，我们可以从这里找到
[Win32 and COM for Metro style apps](http://msdn.microsoft.com/en-us/library/windows/apps/br205757.aspx)  

[http://msdn.microsoft.com/en-us/library/windows/apps/br230232(v=vs.110).aspx .NET for Metro style apps]  

4. WinRT是专门为触屏体验的Metro style Apps实现的一套全新的APIs，但是我们却能从他身上看到很明显的.NET、WPF、Sliverlight的影子，作为系统原生API的WinRT，让我们有理由相信他可以做的比.NET更加优秀；
5. WinRT之上程序员可以使用HTML相关技术来开发应用，可以预见这里肯定会是HTML5+WinRT的一个整合，这也是一个值得我们期待的开发方向。 

# 二、开发环境
2012年2月29号，微软在MWC 2012上发布了Windows 8 Consumer Preview，同时更新了对应的开发工具和开发文档，这些都可以从微软的网站上面找到并下载：  

[Metro style app development](http://msdn.microsoft.com/en-us/windows/apps)

## 使用WinRT进行开发，我们需要：
1. Windows 8 Consumer Preview（截止目前2012/3/30，这是你能下载到的最新的Windows 8）
2. Visual Studio 11 Beta(Ultimate或者Express都可以，但是只有Ultimate才能使用VAssistX)
3. 嗯，没了，要说还有的话就是你需要一个Live ID，在第一次使用VS新建工程时， 你会被要求输入Live ID以获得开发者证书，没有这个证书你将无法进行调试和运行你的代码。

### 想安装Visual Studio 11又不想用微软的在线安装器？
这里提供下微软的Visual Studio 11 Beta Ultimate的离线ISO下载页面地址： 
[Visual Studio 11 Ultimate Beta ISO 离线安装包](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=28975)
 
### 安装后的截图如下：
![2012033015415481.png](http://pic002.cnblogs.com/images/2012/347098/2012033015415481.png)

# 三、Hello WinRT
开始Say hello之前请确认下你是否已经安装好了Windows 8，并在上面安装好了Visual Studio 11，都装好了？哦，让我们开始体验WinRT开发吧  

作为一个Win32程序员，我们最熟悉的自然是C++，所以这里新建了Metro style Apps类型下的C++工程，然后拖了一个TextBlock进来，好了，F5，Hello WinRT  

![2012033016450389.png](http://pic002.cnblogs.com/images/2012/347098/2012033016450389.png)
![2012033016451984.png](http://pic002.cnblogs.com/images/2012/347098/2012033016451984.png)

下一节我将介绍：Windows Store上架前的准备工作