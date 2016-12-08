---
title: 'WinRT for Win32 programmers, Part II-Win32代码移植'
layout: post
tags:
---

> 本文首发于 <http://www.cnblogs.com/linyehui/>   这里是我的Blog备份。

# 引子
说好了这节要介绍Windows Store上架前的准备工作，怎么标题变成了Win32代码移植？这不是坑爹么！  

还真不是，对于Win32程序员来说，提交应用到Windows Store，你可能遇到的最大的问题就是Win32代码移植。  


# 我这么说是有原因的，且听我一一道来，先来看看目前的形势：
1. 我们在Win32下已经积累了多年的代码，如果全部抛掉，在WinRT下重新造一套轮子，SQLite，ZLib这些第三方库且不说，我们和我们公司的祖祖辈辈们一起开发的业务相关的功能库难道也全部重写？这个代价是不是有点大了？
2. WinRT支持部分Win32 API和部分.NET API

想到上面两点，作为一个成熟的Win32程序员，很自然的你会想到，用老的代码稍微组合拼凑下，看看在WinRT下能不能跑起来？  

只要你运气不是太差的话你将发现：新建了个WinRT的工程，把Win32的代码文件拖进去，很轻松的就能编译通过，甚至一次通过，很欢畅的跑起来了呢~  

没白跟着微软混这么多年啊！

但是得但是，你别高兴的太早，因为想要提交到Windows Store，能编译通过还不算数，还要经过微软的“重重审核”，这其中就有一项微软要求开发者在提交应用之前必须自己在本地审核通过的项目：  

**Windows App Certification Kit** Validate Metro style App
![2012050723263913.png](http://pic002.cnblogs.com/images/2012/347098/2012050723263913.png)

如果你的应用无法通过这个认证，就更无法通过微软的审核了，而阻碍你通过认证最大的障碍就来自于Win32移植过来的代码，这也是本文的重点所在。

# 提交Store之前的准备工作之Metro Style Apps 认证：

## 一、如何认证

### 1、制作安装包：
1.1 认证的安装包必须使用Release，使用Debug是无法通过的
1.2 右键主exe工程，选择Store，Create App Package，最后提交Store是需要使用"Create a package to upload to the Store or to use locally"进行打包的，但是认证的话使用"Create a package to use locally only"也是可以的
![2012050723282777.png](http://pic002.cnblogs.com/images/2012/347098/2012050723282777.png)
![2012050723285063.png](http://pic002.cnblogs.com/images/2012/347098/2012050723285063.png)
![2012050723291338.png](http://pic002.cnblogs.com/images/2012/347098/2012050723291338.png)
![2012050723294233.png](http://pic002.cnblogs.com/images/2012/347098/2012050723294233.png)
制作好安装包后，直接点击"Launch Windows App Certification Kit"按钮，就能开始认证了，类似下图，在对你的应用一阵蹂躏后你就能得到认证结果了：
![2012050723362013.png](http://pic002.cnblogs.com/images/2012/347098/2012050723362013.png)
这个方法是最简单的，但有时候会出问题，所以我下面介绍下手动安装和认证的方法

## 2、手动安装
2.1 制作出来的安装包目录下，会有一个Add-AppxDevPackage.bat文件，以管理员权限运行，就能安装了

PS. 虽然这个步骤很简单，但还是可以有些诡异的问题，大家留意下下面这两点

2.2 安装包所在的路径如果包含中文会导致安装失败
2.3 如果你是从别的机器拿过来的安装包，那么你可能由于没有开发者证书而导致安装失败
解决方法：打开VS，新建一个工程，编译时会出现提示让你申请开发者证书，按提示输入Live ID就可以了；
其实还有不用安装VS就可以获取Live ID的方法，这个就另外找地方再给大家介绍了

## 3、手动认证
3.1 从Charm中搜索应用程序，找到Windows App Cert Kit，运行
![2012050723330675.png](http://pic002.cnblogs.com/images/2012/347098/2012050723330675.png)
3.2 选择Validate Metro style App，进入后找到你的应用，选中，然后开始跑认证
![2012050723333844.png](http://pic002.cnblogs.com/images/2012/347098/2012050723333844.png)
点下“Next”，又是一阵蹂躏之后，你就能得到想要的认证结果了
![2012050723340136.png](http://pic002.cnblogs.com/images/2012/347098/2012050723340136.png)

## 二、认证结果的分析
认证过程其实比较简单，认证结果是一个xml文件，我们需要的是全部PASS。
![2012050723380747.png](http://pic002.cnblogs.com/images/2012/347098/2012050723380747.png)
![2012050723345174.png](http://pic002.cnblogs.com/images/2012/347098/2012050723345174.png)

如果有Failed的项就需要根据提示来进行修正，然后重新编包，重新认证，直到认证通过为止。
而今天我们要说的最重要的部分，就在于，认证结果中的Use of Supported Platform 

#### APIs这个部分，如果认证不通过应该怎么处理？
![2012050723384590.png](http://pic002.cnblogs.com/images/2012/347098/2012050723384590.png)
![2012050723385716.png](http://pic002.cnblogs.com/images/2012/347098/2012050723385716.png)

#### 1、为什么认证会不通过
其实字面意思已经很清楚了，就是使用了这些不被支持的API导致的，那究竟哪些函数是被支持的呢？
微软的网站上面有详细的API列表，大家可以查阅：
[Win32 and COM for Metro style apps](http://msdn.microsoft.com/zh-cn/library/windows/apps/br205757)
上面的链接比较分散，我当初在做移植时专门做了个简单的拼接，方便查阅：[[转载](http://www.linyehui.com/win32-and-com-for-metro-style-apps)[合集]Win32 and COM for Metro style apps]

#### 2、怎样认证通过
把不被支持的函数换成被支持的，或者去掉不被支持的函数调用，改用WinRT自己的API。  

说的很简单，有些事情做起来可能那么轻松，我这里列出一些简单的函数移植对应关系：

```
CreateEvent->CreateEventEx
CreateFileW->CreateFile2
CreateMutexW->CreateMutexEx
WaitForSingleObject->WaitForSingleObjectEx
GetTickCount->(DWORD)GetTickCount64
DeleteFileA->DeleteFileW
FormatMessageA->FormatMessageW
GetDiskFreeSpaceA->GetDiskFreeSpaceW
GetFileAttributes->GetFileAttributesEx
GetFileSize->GetFileInformationByHandleEx
GetFullPathName-> :(
GetTempPathA-> :(
GetVersionExW-> :(
InitializeCriticalSection->InitializeCriticalSectionEx
LoadLibrary->don't use this at best,alothough ther is LoadPackagedLibrary can use
LockFile->LockFileEx
UnLockFile->UnLockFileEx
OutputDebugStringA-> don't use
SetFilePointer->SetFilePointerEx
SetThreadPriority-> :(
```

可以看到，不是所有函数都有直接可以替换的函数，更有像socket这类API，整套API WinRT都不允许你使用，这种你只能使用WinRT自己提供的那套新API来替换了。

# 关于Win32下的开源代码库
类似ZLib这种优秀的开源代码，一个字都不用改就能直接在WinRT下编译通过且顺利通过认证，他们的优势在于代码使用纯C标准库实现；
而像SQLite这样的使用了部分Windows API的开源库，想要通过WinRT认证就得费点心思了，好在SQLite团队已经开始进行这件事情，大家着急的话可以自己从这里找到并下载SQLite的WinRT分支代码：<http://www.sqlite.org/src/timeline?r=winrt>  　截至目前(2012/5/7 11:55)SQLite的WinRT分支还没合入trunk

## 自己的开发的代码库就更得自己移植了
我们当时移植的过程中，被折磨的最残的是对socket的支持，上面说到WinRT不允许使用WinSock，而我们的底层是基于异步socket的，为了平滑的移植我们的底层库，我们用StreamSocket这样的“高级”API适配出了“低级”的socket，这个恶心的过程以及这块的代码另外找机会再和大家分享交流。

# 小结：
很多跨平台移植性好的代码还是能很容易的移植到WinRT上，对于Win32程序员来说绝对是个好消息，这里也提醒大家在写跨平台代码时还是尽可能的使用C标准库的函数，这样在进行跨平台移植时才能做到事半功倍。

更详细的提交应用到Windows Store的细节和注意事项，都可以参考微软的官方文档：
Selling apps
Certification requirements for Windows apps