---
title: '文件夹“安全”提示：请求的安全信息不可用或无法显示'
layout: post
tags:
    - wtf
---

> 文件夹“安全”提示：请求的安全信息不可用或无法显示
> The requested security information is either unavailable or cannot be displayed

#### 问题：

用小米盒子迅雷远程下载，文件没有下载完就被我强制拔下了移动硬盘，然后我的移动硬盘上TDDownloader目录下就出现了一堆的“僵尸文件”，打不开，删除掉，恶心死了

#### 问题分析

右键文件的属性，查看文件夹的“安全”属性页，提示：
> 请求的安全信息不可用或无法显示

**尝试过的方法包括以下这些，但不止这些个方法，都不行：**
[暴力删除无法删除的文件](http://ju.outofmemory.cn/entry/13670)  

[WIN7 文件夹“安全”提示：请求的安全信息不可用或无法显示](http://social.technet.microsoft.com/Forums/windows/en-us/b3b1bac4-8d2e-4d1a-a8a3-cfef55a519e2/win7-?forum=window7betacn)  


#### 问题解决

在Mac下直接删除目录，直接成功！  

我在Mac下挂载移动硬盘发现那些文件都看不见了，TDDownloader下是空的，于是尝试直接Command+delete删除了TDDownload目录；  

回到Windows下插上移动硬盘：世界清静了  


> #强迫症是个大问题