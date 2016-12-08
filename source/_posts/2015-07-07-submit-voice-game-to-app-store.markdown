---
title: '一款语音杀人游戏的App Store提交记录'
layout: post
tags:
    - app-store
    - voice-game
    - ios
---

### 第1次提交
**提交类型**：重新提交  

**提交时间**：2015-04-21  

**结果公布**：2015-05-01  

**审核结果**：拒绝  

**拒绝原因**：demo account, 需要测试账号  

**解决方案**：提供测试账号，重新提交  

**相关条款**：  


```
We began the review of your app but are not able to continue because we need a demo account to fully access your app features.
```

### 第2次提交
**提交类型**：重新提交  

**提交时间**：2015-05-01  

**结果公布**：2015-05-13  

**审核结果**：拒绝  

**拒绝原因**：条款2.2，没有用最新Xcode版本打包，且有明显Bug  

**解决方案**：修正Bug，用最新Xcode重新打包，提交  

**相关条款**：  


```
2.2: Apps that exhibit bugs will be rejected 2.2

We found that your app exhibited one or more bugs, when reviewed on iPhone running iOS 8.3 on both Wi-Fi and cellular networks.
Specifically, the status indicator kept spinning indefinitely while logging in. We've attached the sample screenshot(s)
```

### 第3次提交
**提交类型**：重新提交  

**提交时间**：2015-05-13  

**结果公布**：2015-05-22  

**审核结果**：拒绝  

**拒绝原因**：条款17.1，游戏中的排行榜侵犯用户隐私，使用用户数据需要先获得授权  

**解决方案**：游戏登录之前加上隐私声明，不同意不能登录  

**相关条款**：  


```
17.1 - Apps cannot transmit data about a user without obtaining the user's prior permission and providing the user with access to information about how and where the data will be used

17.隐私
17.1 在未经用户事先许可，或未告知用户如何使用信息以及在何处使用信息的情况下，应用程序不能传输用户数据。
```

### 第4次提交
**提交类型**：重新提交  

**提交时间**：2015-05-22  

**结果公布**：2015-06-02  

**审核结果**：拒绝  

**拒绝原因**：  

条款2.2，游戏Bug，充值后金币没有刷新  

条款2.8，设置页面有个二维码，二维码扫描后是fir.im的下载页面  

条款17.2，登录必须使用YY登录，需要解释为什么必须使用第三方账号，或者提供“游客模式”  


**解决方案**：  

2.2，游戏Bug，修正游戏Bug，热更新游戏解决
2.8，去掉二维码，改成Logo，热更新游戏解决
17.2，写段申诉描述，主要说我们使用了YY的实时语音功能，需要接入YY账号

##### 这个版本的问题都可以通过热更新解决，所以使用申诉而不是重新提交：
```
2.2 - Apps that exhibit bugs will be rejected
2.8 - Apps that install or launch other executable code will be rejected
--------------------
These are Bugs in our server, we have fixed them without changing the APP, please re-confirm this error, Thank you.

17.2 - Apps that require users to share personal information, such as email address and date of birth, in order to function will be rejected
--------------------
This APP is a real-time voice communication social game. 
This APP relies on real-time voice communication function which is provided by YY account, so we need user registration prior to using real-time voice communication.

Take YY (https://itunes.apple.com/en/app/yy-zui-huo-shi-pin-zhi-bo/id427941017?mt=8) as a reference, when user using YY, they can watch videos without login, but if they want to chat with friends, it is necessary to registration prior and login wity YY account.

Thank you for reviewing our APP again.
```


**相关条款**：  


```
2.2 Apps that exhibit bugs will be rejected
2.8 Apps that install or launch other executable code will be rejected
17.2: Apps that require users to share personal information, such as email address and date of birth, in order to function will be rejected
```

### 第5次提交
**提交类型**：申诉  

**提交时间**：2015-06-02  

**结果公布**：2015-06-04  

**审核结果**：拒绝  

**拒绝原因**：条款2.16，VoIP权限  

**解决方案**：去掉VoIP的权限  

**相关条款**：  


```
2.16 Multitasking Apps may only use background services for their intended purposes: VoIP, audio playback, location, task completion, local notifications, etc.
```

### 第6次提交
**提交类型**：重新提交  

**提交时间**：2015-06-04  

**结果公布**：2015-06-15  

**审核结果**：拒绝  

**拒绝原因**：条款2.2，游戏Bug，金币充值失败  

**解决方案**：修正游戏Bug，发布热更新后，提交申诉  

**相关条款**：  


```
2.2 Apps that exhibit bugs will be rejected
```

### 第7次提交
**提交类型**：申诉  

**提交时间**：2015-06-15  

**结果公布**：2015-06-19  

**审核结果**：拒绝  

**拒绝原因**：条款2.2，1.拍视频解释为什么要使用Background Audio权限；2，标题中的“美女陪你放纵”要去掉  

**解决方案**：1.去掉Background Audio权限；2，去掉标题和描述中可能有问题的文字  

**相关条款**：  


```
2.2 Apps that exhibit bugs will be rejected
```


### 第8次提交
**提交类型**：申诉  

**提交时间**：2015-06-23（19号提交了一个版本但有问题，23号撤回后重新提交）  

**结果公布**：2015-07-01 9:00  

**审核结果**：审核通过  

**拒绝原因**：无  

**解决方案**：无  

**相关条款**：无  



### 参考：
[App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)