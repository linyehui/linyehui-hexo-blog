---
title: 'Xcode 7.1无法使用Instruments真机调试，设备灰色不可选'
layout: post
tags:
    - mac
    - ios
    - xcode
---

## 问题描述

### 开发环境
Macbook Pro + Mac OS X 10.11.1 + Xcode 7.1 +Instruments 7.1  

iPhone 6 + iOS 9.1  


### 问题描述
![Instruments中的设备选择无法选择iPhone真机，灰色不可选](/media/files/2015/11/05/instruments-gray-device.png)

Instruments中的设备选择无法选择iPhone真机，灰色不可选


### 解决方案：重启
我最终的解决步骤：  

1、拔掉iPhone的USB线，重启iPhone  

2、关闭Xcode和Instruments  

3、重新连接iPhone到Mac上  

4、重启Xcode并启动Profile  

5、成功  


## 参考
这个帖子的方法管用，我的iPhone 6 9.1系统可以正常调试起来了  

[unable-to-profile-app-on-device-with-ios-9-0-1-using-xcode-7-7-0-1-or-7-1-beta](http://stackoverflow.com/questions/32878283/unable-to-profile-app-on-device-with-ios-9-0-1-using-xcode-7-7-0-1-or-7-1-beta)
