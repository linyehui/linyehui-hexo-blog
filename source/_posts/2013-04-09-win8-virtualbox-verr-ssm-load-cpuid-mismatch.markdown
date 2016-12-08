---
title: 'VirtualBox在Win8下的VERR_SSM_LOAD_CPUID_MISMATCH问题'
layout: post
tags:
    - virtualbox
---

**问题描述：**  

之前都好好的，今天自己不记得做了什么操作后（后面我才想起来），我的VirtualBox中的虚拟机要么启动到一半就卡住，要么就提示：  

中文：  

> 不能为虚拟电脑 UbuntuDesktop 打开一个新任务.
> 
> cpum#1: X86_CPUID_FEATURE_ECX_MONITOR mismatch: host=0 saved=1 [ver=14 pass=final] (VERR_SSM_LOAD_CPUID_MISMATCH).
> 
> 返回 代码:E_FAIL (0x80004005)
> 组件:Console
> 界面:IConsole {db7ab4ca-2a3f-4183-9243-c1208da92392}

英文：  

> Failed to open a session for the virtual machine UbuntuServerJP.
> 
> cpum#1: X86_CPUID_FEATURE_ECX_MONITOR mismatch: host=0 saved=1 [ver=14 pass=final] (VERR_SSM_LOAD_CPUID_MISMATCH).
> 
> Result Code: E_FAIL (0x80004005)
> Component: Console
> Interface: IConsole {db7ab4ca-2a3f-4183-9243-c1208da92392}

**寻找原因：**  

我感到非常的奇怪，一阵狂轰滥炸之后我突然想起了关键字CPU，所以我打开了“securable.exe”，结果如我所料的是虚拟化技术不支持！  

这个太奇怪了，明明是支持的，而且前两天都还好好的，怎么会突然这样呢？

于是我再次Google，Win8 虚拟化，结果一个似曾相识的关键字映入我的眼帘：“Hyper-V"  

我好像想起了什么？！是的我早上的时候在网卡适配器那里看到几个Hyper-V的虚拟化网卡，我以为是VirtualBox的，所以我就从设备管理器里面把他给卸载掉了  

好吧，看来问题就出在这个Hyper-V上面，而且是我把他给破坏了

**知道了原因，解决方案也是水落石出：**  

1、到控制面板中“启用或关闭 Windows功能”，找到Hyper-V，去掉勾选，确认，重启  

2、重新打开“securable.exe”，OK，最后一个选项正常了  

3、重新打开VirtualBox，一切都正常了  


**参考信息：**  

[将 Hyper-V 引入“Windows 8”](http://blogs.msdn.com/b/b8_cn/archive/2011/09/12/hyper-v-windows-8.aspx)  

[Windows 8自带虚拟机Hyper-V，VMware靠边站](http://soft.cfan.com.cn/apps/201301/07/1357549782d99787.shtml)  

[VirtualBox32位系统上安装64位系统](http://blog.csdn.net/mal327/article/details/6597263)  
