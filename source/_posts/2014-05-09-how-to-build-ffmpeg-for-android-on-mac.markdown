---
title: '在Mac上编译FFmpeg的Android版本'
layout: post
tags:
    - android
    - ffmpeg
---

How to Build FFmpeg for Android On Mac

官方文档在这：[CompilationGuide/Android](https://trac.ffmpeg.org/wiki/CompilationGuide/Android)  

关于Android上的FFmpeg入门，这个Blog不错：[roman10 ffmpeg on Android](http://www.roman10.net/category/android-tutorial/ffmpeg-on-android/)  


# 关于FFmpeg耻辱名单

使用开源代码，第一步一定是确认开源协议所带来的风险，确认后我们才能决定是否使用这一开源代码。

特别是暴风影音和QQ影音让大家知道了FFmpeg的耻辱名单：[Hall of Shame](http://ffmpeg.org/shame.html)，我们更得先研究下FFmpeg的开源协议了。

被加入这个名单的直接原因是这些产品违反了FFmpeg的开源协议，但具体是违反了那个开源协议呢？这就得从[FFmpeg的开源协议](https://ffmpeg.org/legal.html)说起了:
> FFmpeg is licensed under the GNU Lesser General Public License (LGPL) version 2.1 or later. However, FFmpeg incorporates several optional parts and optimizations that are covered by the GNU General Public License (GPL) version 2 or later. If those parts get used the GPL applies to all of FFmpeg.

### 怎么的就违反开源协议了呢？

1. 虽然FFmpeg使用的开源协议是LGPL，但是他用到的部分组件是GPL开源协议;
2. LGPL和GPL有着很大的区别，这个可以参考：[关于开源授权协议 GPL 和 LGPL](http://www.oschina.net/question/12_2663)；
简单来说，如果商用产品只是把LGPL的开源代码作为第三方库来使用，商用产品本身是不需要开源的，但GPL就不一样，只要你用了开源代码，整个商用产品就必须开源，这就是常说的“传染性”；
3. 如果使用FFmpeg没有使用到GPL开源协议的部分组件，那么产品只要遵循LGPL就可以了，哪怕你修改了第三方库，也只需要开源修改后的第三方库代码就不算违反LGPL；
4. 如果使用到了GPL开源协议的部分组件，如x264，那么商用产品就必须遵循GPL，开源商用产品的所有代码；
5. 综合上面4点，可以知道如果使用了FFmpeg中使用GPL的部分组件，那么你的商用产品就必须开源，否则就是违反了GPL开源协议，要被加入FFmpeg的Hall of Shame。

一不小心就上了耻辱名单，有没有什么防范措施呢？可以参考FFmpeg官方的[License Compliance Checklist](https://ffmpeg.org/legal.html)  


# 在Mac下编译FFmpeg-2.1.4

### 概要说明

Mac开发环境，按照这篇文章来的指引编译通过：[How to Build ffmpeg with NDK r9](http://www.roman10.net/how-to-build-ffmpeg-with-ndk-r9/)  


直接按照文章的指引编译通过，只有一个步骤需要特别注意，build_android.sh中：

> TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.8/prebuilt/linux-x86_64
> 
> 改成
> 
> TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64

./build_android.sh中的路径要从linux-x86_64改成darwin-x86_64，这个我是在编译错误的时候根据提示发现下面这个路径文件找不到，才知道需要改下，修改之后就可以继续调用./build_android.sh编译了

## 详细编译步骤

虽然都是按照[How to Build ffmpeg with NDK r9](http://www.roman10.net/how-to-build-ffmpeg-with-ndk-r9/)中的指引来操作的，我还是把操作流程简单记录如下：
1. 我的开发环境
> Mac OSX 10.9.2
> XCode 5.0.2(带的gcc)
> NDK r9d

2. 下载并配置好[NDK](http://developer.android.com/tools/sdk/ndk/index.html)，我之前已经配好了，不清楚的同学自己看Android的官网帮助文档 
3. 下载[ffmpeg源码](http://www.ffmpeg.org/download.html)，我下载时的最新稳定版本是2.1.4，解压到NDK目录下的sources目录下
4. 修改ffmpeg-2.1.4目录下的configure文件
Open ffmpeg-2.0.1/configure file with a text editor, and locate the following lines.
> SLIBNAME_WITH_MAJOR='$(SLIBNAME).$(LIBMAJOR)'
> LIB_INSTALL_EXTRA_CMD='$$(RANLIB) "$(LIBDIR)/$(LIBNAME)"'
> SLIB_INSTALL_NAME='$(SLIBNAME_WITH_VERSION)'
> SLIB_INSTALL_LINKS='$(SLIBNAME_WITH_MAJOR) $(SLIBNAME)'

    This cause ffmpeg shared libraries to be compiled to libavcodec.so.<version> (e.g. libavcodec.so.55), which is not compatible with Android build system. Therefore we’ll need to replace the above lines with the following lines.
> SLIBNAME_WITH_MAJOR='$(SLIBPREF)$(FULLNAME)-$(LIBMAJOR)$(SLIBSUF)'
> LIB_INSTALL_EXTRA_CMD='$$(RANLIB) "$(LIBDIR)/$(LIBNAME)"'
> SLIB_INSTALL_NAME='$(SLIBNAME_WITH_MAJOR)'
> SLIB_INSTALL_LINKS='$(SLIBNAME)'

5. 新建build_android.sh，下面是使用的配置，主要是开头的三个变量要根据自己机器上的情况修改成对应的路径:

    #!/bin/bash
    NDK=/Applications/android-ndk-r9d
    SYSROOT=$NDK/platforms/android-9/arch-arm/
    TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64
    function build_one
    {
    ./configure \
        --prefix=$PREFIX \
        --enable-shared \
        --disable-static \
        --disable-doc \
        --disable-ffmpeg \
        --disable-ffplay \
        --disable-ffprobe \
        --disable-ffserver \
        --disable-avdevice \
        --disable-doc \
        --disable-symver \
        --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
        --target-os=linux \
        --arch=arm \
        --enable-cross-compile \
        --sysroot=$SYSROOT \
        --extra-cflags="-Os -fpic $ADDI_CFLAGS" \
        --extra-ldflags="$ADDI_LDFLAGS" \
        $ADDITIONAL_CONFIGURE_FLAG
    make clean
    make
    make install
    }
    CPU=arm
    PREFIX=$(pwd)/android/$CPU 
    ADDI_CFLAGS="-marm"
    build_one


    我一开始没留意TOOLCHAIN的路径，后面发现编译错误，才修改了build_android.sh中TOOLCHAIN的路径定义：
> TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.8/prebuilt/linux-x86_64
> 
> 改成
> 
> TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64

6. 保存build_android.sh，并调用执行编译
我直接把build_android.sh保存在了ffmpeg-2.1.4根目录下，然后执行：
> sudo chmod +x build_android.sh
> ./build_android.sh

7. 编译通过，在ffmpeg-2.1.4/Android目录下生成了若干.so文件

## 解压到别的目录下去编译

文章中把ffmpeg的源代码解压到了NDK的sources目录下，并说明了原因：
> The reason we built our ffmpeg source code under $NDK/sources folder is that NDK build system will search for directories under this path for external modules automatically. 

其实解压到哪里编译都可以，只是放在NDK目录下其他工程使用ffmpeg时的引用路径配置方便一点。

-- The End --