---
title: 'Windows下的SDK Manager.exe闪退'
layout: post
tags:
    - windows
    - sdk-manager
    - android
---

## 问题描述
双击SDK Manager.exe，闪退；  

网上找了下资料，SDK Manager.exe启动其实调用的是android.bat这个批处理，而闪退的直接原因就是find_java.bat执行没有获取到正确的java.exe路径

### 问题根源
问题的根源在于android.bat中的这一段脚本执行出错了，没有获取到正确的java.exe路径  


```bat
rem Check we have a valid Java.exe in the path.
set java_exe=
call lib\find_java.bat
if not defined java_exe goto :EOF
```

其实问题最终的根源在于find_java.bat中调用的find_java.exe没有返回正确的路径

## 解决方案

### 简单粗暴的解决方案
* 简单的改法就是修改Android.bat中的java_exe路径，直接写死；  

* 当然这是个非常粗暴的解决方案，方案的风险在于：下次你复制这个目录到别的电脑上使用的时候，这里设置的路径需要重新设置。

```
set java_exe=C:\Program Files\Java\jdk1.7.0_40\bin\java.exe
```

### 正确的解决方案
其实这个更简单，在系统环境变量中，添加一个JAVA_HOME的变量定义  


```
JAVA_HOME
C:\Program Files\Java\jdk1.7.0_40
```

#### 验证的方法
直接调用下find_java.exe看看能否返回正确的路径。

### 参考
[64位win7下Android SDK Manager闪退的解决方法](http://blog.csdn.net/puma004/article/details/11392271)  

[解决64位Win7下的android sdk manager一闪而过的问题](http://blog.sina.com.cn/s/blog_6e1ad0cf01013v9u.html)  

[安装Android SDK时，点击SDK Manager.exe闪退](http://blog.sina.com.cn/s/blog_8a2d5e270101ad3q.html)  



