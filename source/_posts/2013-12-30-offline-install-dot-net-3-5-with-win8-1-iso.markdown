---
title: 'Win8.1下使用系统ISO离线安装.NET 3.5'
layout: post
tags:
    - .net
    - vs2008
    - win8
---

Offline Install .Net 3.5 With Win8.1 ISO

# 问题：


Windows 8.1下安装VS2008，提示缺少.NET 3.5，但是.NET 3.5在Win8下属于系统自带功能，无法直接使用离线安装包安装，只能通过“启用或关闭Windows 功能”来打开
> 控制面板 -> 程序和功能 -> 启用或关闭Windows 功能

但是坑爹的是“启用或关闭Windows 功能”竟然是在线去下载，我这网速下200多MB的安装包都不知道要几天

# 解决方案：


#### 思路：


还好发现Win8下可以使用系统安装包ISO中的安装源来安装，直接用ISO来安装就不用等待下载了

#### 操作步骤：


1、直接右键装载Win8.1系统安装盘ISO（我这里装载后的盘符是E:）  

2、从系统Charm菜单上选择搜索，输入CMD，然后右键选择“以管理员权限打开”  

3、命令行下输入（注意，E:来自于第一步装载ISO后的盘符）：
> dism.exe /online /enable-feature /featurename:NetFX3 /Source:E:\sources\sxs

4、成功后提示
> 启用一个或多个功能
> [=========================100%=========================]
> 操作成功完成。

5、去“启用或关闭Windows 功能”确认下".NET Framework 3.5(包括.NET 2.0 和 3.0)"前面的CheckBox已经勾选，成功 :)
> 控制面板 -> 程序和功能 -> 启用或关闭Windows 功能

PS.  

我的安装VS2008失败的问题也同时解决了，重新安装一次，成功 :)