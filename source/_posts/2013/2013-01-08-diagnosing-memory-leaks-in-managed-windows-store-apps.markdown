---
title: 'Windows Store Apps内存泄漏分析之SOS.dll'
layout: post
tags:
---

## 1. 环境准备

### 1.1 在Win8下要用x86的Windbg才能成功load起sos.dll，抓取Dump的工具也必须是x86的版本，否则就就会出现下面两种错误：

#### error： x64的Windbg无法成功load sos.dll
> 0:000> .load sos.dll
> The call to LoadLibrary(sos.dll) failed, Win32 error 0n193
>     "%1 不是有效的 Win32 应用程序。"
> Please check your debugger configuration and/or network access.

#### error: x64的TaskManager或者Procexp创建的Dump不支持SOS.dll，我最后成功的是用x86的Windbg 抓的
> 0:000> !DumpHeap -stat
> SOS does not support the current target architecture.
> 

### 1.2 Windbg
直接到微软下载就可以了：<http://msdn.microsoft.com/en-US/windows/hardware/gg463009/>  


### 1.3 SOS.dll
文档中使用了[SOS.dll (SOS Debugging Extension)](http://msdn.microsoft.com/en-us/library/bb190764.aspx)，使用之前需要先用下面的命令加载下sos.dll

```shell
.load <full path of sos.dll>
```
  

  

## 2. 遇到的其他错误

### 2.1 Failed to find runtime DLL (clr.dll), 0x80004005
> 0:000> .load sos.dll
> 0:000> !dumpheap -stat
> Failed to find runtime DLL (clr.dll), 0x80004005
> Extension commands need clr.dll in order to have something to do.

### 2.2 解决方案：
App必须是：Managed Windows Store Apps，所以对我们来说SOS.dll这个方向一开始就是错的，汗

### 2.3 参考：  

[Load SOS in WinDbg](http://mylittlereminder.wordpress.com/2011/07/08/windbg-load-sos-in-windbg-0x80004005/)  

[使用WinDbg调试Silverlight中遇到“Failed to find runtime DLL (clr.dll), 0x80004005”问题的解决方法之一](http://blog.csdn.net/neils03/article/details/7928641)  


  

## 3. 小结

非Managed的App无法使用这个方案，这对我们的应用就没有意义了，但是解决前面遇到的几个小问题的方法希望对其他朋友有所帮助。


## 4. 参考文档：
[Diagnosing Memory Leaks in Managed Windows Store Apps](http://blogs.microsoft.co.il/blogs/sasha/archive/2012/10/15/diagnosing-memory-leaks-in-managed-windows-store-apps.aspx)
