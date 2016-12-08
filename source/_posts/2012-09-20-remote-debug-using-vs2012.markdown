---
title: '用VS2012远程调试Metro style apps'
layout: post
tags:
    - remote-debug
    - vs2012
    - win8
    - winrt
---

# 我的问题：
环境：  

Windows 8 版本 RTM (Build9200) + Visual Studio Ultimate 2012(Version 11.0.50722.1)

我希望在同一个无线网络下的PC和平板之间远程调试我们的Metro 应用，PC上安装了VS2012，平板上没有安装VS2012，但是遇到了些问题

# 解决方案：
* 1、在平板上安装远程调试工具包：rtools_setup_x64.exe
* 2、在平板上运行并配置Remote Debugger
	* 2.1 记得以管理员权限打开Remote Debugging Monitor
	* 2.2 配置认证方法，使用“No Authenticaton”并勾选"Allow any user to debug"

* 3、在PC上，打开VS，菜单"Tools"->"Attach to Process"，选择Remote，然后输入平板的IP地址，随便选择一个进程进行attach**
* 4、必须要有第三步，因为我发现我从PC竟然ping不到平板的IP，但是却可以用第三步attach到平板上的进程
* 5、取消第三步的attach，打开你需要远程调试的工程，Debug Target选择“Remote Machine”，然后F5
* 6、搞定

关键在于远程之前先attach下，应该是VS的一个Bug，不知道新版本是否解决了，这里备忘下