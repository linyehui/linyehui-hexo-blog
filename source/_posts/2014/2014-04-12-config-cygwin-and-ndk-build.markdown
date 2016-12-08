---
title: 'Windows下配置cygwin和ndk编译环境'
layout: post
tags:
    - cygwin
    - ndk
---

# cygwin安装


> 正确的安装步骤其实很简单：
> 1. 下载setup-86_64.exe
> 2. 直接从网上下载安装，选择包时，顶部选择“default”不变
> 3. 搜索make，勾选make，cmake，emacs-cmake,libjepg
> 4. Shells这个二级目录选择“install”
> 5. 下一步，开始安装
> 6. 安装完成后调用下make命令看下是否已经安装成功（其实最直接的是测试下ndk-build）
> 7. 上面提到的这些包一起，下载后的目录是82MB左右，下载了的话肯定是哪里配置错了，不需要那么多东西。

#### 结论很简单，不过我折腾的过程就有点心酸了：

##### 1.坑爹的安装界面交互


下载setup-x86_64.exe，出现包选择的时候，我点击了下“default”，变成了“install”，那时我像，哦～应该是这样吧，然后我就点了下一步，结果我下了差不多半天，下完发现那个有2G多还是3G，什么乱七八糟的都给我下载下来了。

这个方法明显不对，所以我过段把下载完的东西全删了。

##### 2.默认安装


其实打开setup-x86_64.exe后，选择包的那个界面，保持根上的那个“default”不动，就是默认安装，默认安装几分钟就装好了

##### 3.缺什么包选什么包


默认安装后测试下make命令会发现，make都没有安装；
这个时候也好办，提示你缺什么包，你就回到setup-x86_64.exe，在包选择界面filter进行查找，然后勾选，下一步安装。

测试总结之后，我发现把这几个勾选上，基本的ndk-build已经够用了：
> make，cmake，emacs-cmake,libjepg,Shells整个二级目录
这些个下载下来的文件也就82MB多。

##### 4.测试安装是否正常


因为ndk-build会用到make，所以安装完cygwin后，桌面快捷方式打开cygwin，然后测试下make命令，如果提示正常，那就是cygwin安装成功了。


##### 5.不要装写没用的，用到什么装什么

cygwin安装成功的话setup-x86_64.exe是不会有什么错误提示的，如果你装的包越多，cygwin对系统的依赖也就越多，反而会出些不兼容的安装错误。

一开始的时候会提示一个bash错误，后面我发现是我装msysgit时带的bash冲突了，我就先吧msysgit卸载了再安装cygwin就好了。
然后重新安装msysgit时记得选择默认的bash选项：Git Bash only

网上还有文章说ndk编译需要依赖devel和editors，要是傻傻勾选了这两个包目录，那就有得你下载了，其实只需要其中的make相关的几个包就够了，不用整个二级目录都“install”。

Shells目录因为比较小，直接勾选上问题不大。

#### 参考文章：

[android-ndk-r7b编译环境Cygwin工具搭建及配置](http://my.oschina.net/tonywolf/blog/49214)

# ndk-build解压并配置

1. 解压ndk压缩包
2. 配置环境变量，在C:\cygwin64\home\linyehui\.bash_profile最后添加两行
> export ANDROID_NDK_HOME=/cygdrive/c/android-ndk-r9c
> export PATH=$ANDROID_NDK_HOME:$PATH

3. 测试ndk-build
bash下直接测试下ndk-build命令，提示正常的话就是安装成功了。

# 小结

网上的教程挺多的，但没有一个写的特别明白，大多都是让你“全部安装”，或者勾选一大堆，用个cygwin要下载1～2G！这就像电脑开机有点慢，有人跟你说重装系统！！

写这个文章也是希望自己做每个事情都能认真的琢磨透，而不是为了达到目的敷衍了事，回头一遇到点小变化，就还得折腾很久，还不如一次搞定，这才是聪明的懒人。