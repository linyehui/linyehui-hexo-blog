---
title: 'CocoaPods was not able to update the `master` repo'
layout: post
tags:
    - cocoapods
    - ios
---

# 遇到的问题
系统升级到OS X 10.10.1 + CocoaPods 0.34后遇到了下面的问题，之前是一切正常的：  


>[!] The use of implicit sources has been deprecated. To continue using all of the sources currently on your machine, add the following to the top of your Podfile:
>
>    source 'https://github.com/CocoaPods/Specs.git'
>
>
>[!] CocoaPods was not able to update the `master` repo. If this is an unexpected issue and persists you can inspect it running `>pod repo update --verbose`

# 解决思路

## 尝试1：重装CocoaPods，问题依旧
```
gem list --local | grep cocoapods

# 好几个，全部uninstall
gem uninstall cocoapods

gem install cocoapods
```

## 尝试2：清除CocoaPods缓存后重新pod setup，解决问题
```bash
sudo rm -fr ~/.cocoapods/repos/master
pod setup
```

# 原因分析
解决问题后再次搜索总是能得到正确答案:  

[I'm getting permission errors while running pod commands](http://guides.cocoapods.org/using/troubleshooting.html#im-getting-permission-errors-while-running-pod-commands)

简单来说就是0.32之后的CocoaPods repo目录访问现在不需要root权限了，之前版本生成的目录权限在新版本下会有权限问题，所以需要删除重新生成一次。

