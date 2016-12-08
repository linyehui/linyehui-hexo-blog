---
title: 'cocos2d-js 配置NDK调试'
layout: post
tags:
    - ndk
    - cocos2d-js
    - cocos2d
    - android
---

## 需求
cocos2d-js-v3.1生成的工程，添加了C++的代码，想要调试C++部分的代码，所以需要在cocos console或者cocos code IDE生成的工程上配置NDK调试，以便我们可以Debug As->Android Native Application。

## 解决过程 

#### 环境配置
Windows跟Mac下都统一使用ADT，除了路径格式有点区别，其他的配置都是一样的。

```
Android Developer Tools
Build: v22.3.0-887826

NDK 
D:\android-ndk-r9d

```
为了说明这个问题，我用cocos code IDE创建了一个工程NativeDemo，下面中的.\NativeDemo就是这个工程的根目录

#### Add Native Support

##### 默认生成的工程是找不到Add Native Support的
用ADT打开工程后，没有办法使用Native来调试，这样就没办法调试NDK了，排查了下：  

1.右键工程Android Tools中没有Add Native Support菜单  

2.到工程属性中查看，有C/C++ Build这个Tab页，但是点开却提示这不是一个CDT工程  

3.对比之前的CDT工程，发现了下面的区别，而根目录下并没有.cproject文件存在：  

![cproject](/media/files/2015/03/17/cproject.png)

##### 使用cocos console和cocos studio创建的工程有什么不同
首先，这两种方式创建的工程，都有上面的这个问题，也就是缺失了.cproject这个文件；  

除此之外cocos console创建的工程是个empty project，而cocos studio创建工程的时候会有一个简单的向导，帮你生成第一个页面。

##### 出现这个问题的原因：
工程创建的时候生成的.project已经添加了"Add Native Support"支持，但是同时生成的.cproject文件却没有入库，从而导致了上面的问题

##### 解决方案：
打开.\NativeDemo\frameworks\runtime-src\proj.android\.project，查找并删除cdt相关的两部分配置：
1.BuildCommand  

2.natures

删除成功的标准就是右键工程-》Android Tools的菜单下可以看到Add Native Support；    

另外删除成功后工程属性下也看不到C/C++ Build的Tab页；    

而工程属性的Builder下会看不到 Scanner Configuration Builder。    


修改好了之后，右键工程，Android Tools，Add Native Support 

##### 参考
[How to build and run HelloWorld on Android NDK r5 and above](http://www.cocos2d-x.org/wiki/How_to_build_and_run_HelloWorld_on_Android_NDK_r5_and_above)  

[无需cygwin，使用NDK进行开发](http://www.cnblogs.com/sw926/p/3232311.html)  



#### 配置NDK build

##### 添加一个C++变量

```
COCOS2DX_HOME
../../js-bindings/cocos2d-x
```

![build var](/media/files/2015/03/17/build_var.png)

##### 配置NDK build的命令行
```
#Windows
ndk-build NDK_DEBUG=1 -C "${ProjDirPath}" "NDK_MODULE_PATH=${COCOS2DX_HOME}/..;${COCOS2DX_HOME};${COCOS2DX_HOME}/external;${COCOS2DX_HOME}/cocos;../Classes"

#Mac
ndk-build NDK_DEBUG=1 -C "${ProjDirPath}" "NDK_MODULE_PATH=${COCOS2DX_HOME}/..:${COCOS2DX_HOME}:${COCOS2DX_HOME}/external:${COCOS2DX_HOME}/cocos:../Classes"

```
![ndk build](/media/files/2015/03/17/ndk_build.png)

* NDK_DEBUG=1这个参数记得加上

### R_ARM_THM_CALL
C++编译过程中出现
```
Failed Android build: “error: relocation overflow in R_ARM_THM_CALL”
```

##### 解决方案：
/runtime-scr/proj.android/jni/Android.mk 中添加一行：
> LOCAL_ARM_MODE := arm

参考：[http://blog.csdn.net/mingzznet/article/details/41120551](http://blog.csdn.net/mingzznet/article/details/41120551)

### SDK版本号导致的Unknown Application ABI
```
Unknown Application ABI: 
Android NDK: WARNING: APP_PLATFORM android-19 is larger than android:minSdkVersion 9 in ./AndroidManifest.xml
```
#### 解决方案：
手机上的版本需要和安装了的SDK已经配置的SDK版本一致，以我的小米2S为例，我的系统版本是4.1，也就是对应SDK 的API Level 16；  

下面这两个文件需要对应的都修改成16：  

##### AndroidManifest.xml
```
<uses-sdk android:minSdkVersion="16"/>
```
##### project.properties
```
target=android-16
```


### NDK_MODULE_PATH
切换到Mac下，使用SDK 16来编译，提示NDK Module找不到，想起来之前做过类似的事情，NDK build命令行设置的NDK_MODULE_PATH适用于编译，但是调试的时候还是会提示找不到路径，解决方案是在Android.mk中加上调试用的配置，不调试的时候关掉：

```
# 调试NDK用的定义，平常不要打开 linyhui
$(call import-add-path, ../../js-bindings)
$(call import-add-path, ../../js-bindings/cocos2d-x)
$(call import-add-path, ../../js-bindings/cocos2d-x/external)
$(call import-add-path, ../../js-bindings/cocos2d-x/cocos)
$(call import-add-path, ../Classes)
```

[import-module的注意事项与NDK_MODULE_PATH的配置](http://blog.sina.com.cn/s/blog_4057ab62010197z8.html)  


```
如果NDK_MODULE_PATH 没有设置或者设置不正确。编译时都是报错 Are you sure your NDK_MODULE_PATH variable is properly defined。
```


### bindings目录下的Android.mk有语法错误
排查了半天发现是这个文件有语法错误，导致无法调试，但不知道为什么编译是没有问题的，这个算是cocos2d的一个Bug：

```
./NativeDemo/frameworks/js-bindings/bindings/Android.mk

#### 注释掉这个文件末尾中的这一行：

#$(call import-module,.)
```

注释后，Build下C++，就可以Debug As->Android Native Application

### 搞定了
可以Debug As->Android Native Application