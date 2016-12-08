---
title: '如何调试NDK'
layout: post
tags:
    - android
    - ndk
---

使用C++编写跨平台lib，提供给iOS和Android使用，那么NDK是必然要用到的，那么怎么调试NDK呢？
网上的教程挺多的，但是跟着做下来后还是遇到了不少诡异的问题，对于Android新手的我来说还是有点摸不着头脑，解决后在这备忘下。

#### 我遇到的问题

首先我照着这个教程:[利用JNI技术在Android中调用、调试C++代码](http://blog.micro-studios.com/?p=4212)写了个HelloWorld，而且可以“Debug As Android Application”跑起来，但是我想要“Debug As Android Native Application” 的时候就遇到了下面这些问题：

##### 1.提示 Unknown Application ABI: 

你会发现Console中有很多行这个提示，但是其实这个提示是可以先忽略掉的，你可以拿个文本编辑器把重复的这些提示过滤掉，这样你就能看到真正的错误提示了；
我看到的是我的第二个问题

##### 2.提示 Android NDK: WARNING: APP_PLATFORM android-19 is larger than android:minSdkVersion 16

解决方案1，这个提示我暂时还没弄明白原理，但是有个最简单的方法来解决：
到AndroidManifest.xml中把这两个字段都改成最大的版本号
> android:minSdkVersion="19"
> android:targetSdkVersion="19"

解决方案2：
到SDK Manager中去下载对应版本的Android SDK Build-tools和SDK Platform

##### 3.模拟器调试

自带的模拟器有多慢大家肯定是知道的，方便调试我使用的是Genymotion，但是后面我发现Genymotion是不支持Debug As Android Native Application的，会提示下面这个，暂时还没找到问题的解决方案：
> [2014-03-23 17:28:00 - HelloNDK] Unable to find a compatible ABI
> [2014-03-23 17:28:00 - HelloNDK] ABI's supported by the application: armeabi
> [2014-03-23 17:28:00 - HelloNDK] ABI's supported by the device: x86, armeabi-v7a

##### 4.提示

[2013-06-25 15:25:22 - TestNDK2] gdbserver output:
[2013-06-25 15:25:22 - TestNDK2] run-as: exec failed for lib/gdbserver Error:No such file or directory
[2013-06-25 15:25:22 - TestNDK2] Verify if the application was built with NDK_DEBUG=1

**解决方案：**  

1.确认编译选项是否加了NDK_DEBUG=1  

2.已经加了还是不行，终端中输入下adb看看是不是adb命令不可用，是的话，配置下.bash_profile，把adb的路径加进去，加PATH可以参考[这个](http://gushedaoren.blog.163.com/blog/static/17366340520124300630343/)  

3.用这个命令把gdb_server push到Android手机或模拟器上  

> adb push /Applications/android-ndk-r9d/prebuilt/android-arm/gdbserver/gdbserver /data
参考这个：[通过gdbserver实现远程调试](http://hi.baidu.com/linuxcfan/item/4d6a07ee9fcdb2255b2d64df)   


##### 5.NDK调试提示错误：

> No symbol table is loaded.  Use the "file" command.  

 
使用ndk-gdb --start 启动程序第一个activity，但是此时so文件并没有被加载。解决方法：首先打开程序并使用，在保证so文件已经被使用的情况下，调用ndk-gdb命令调试程序进程。
正确流程：1.创建一个模拟器或者使用真机运行想要调试的程序，确保so文件已经被加载。
一个比较简单的方法，在load so库的java文件中添加一个无关紧要的方法，比如：
Java代码  收藏代码
public static void test1(){  

    Log.i(tag, "test1");  

}  

然后在主activity起来的时候，调用一下这个方法就OK了。
参考这篇文章：[android NDK开发、编译、调试环境搭建与操作入门](http://qiang106.iteye.com/blog/1830416)  


#### 参考：

网上的教程挺多的，我参考了下面这几篇：
[Android官网](http://developer.android.com/tools/sdk/ndk/index.html)  

[超简单的NDK单步调试方法](http://blog.csdn.net/wutianyin222/article/details/8222838)  

[eclipse下ndk调试](http://blog.csdn.net/wjr2012/article/details/7993722)  

[【暗黑世界】NDK调试的设置方法](http://www.aiseminar.cn/bbs/home.php?mod=space&uid=3&do=blog&id=2735)  
