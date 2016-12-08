---
title: 'adb shell下使用vi修改hosts'
layout: post
tags:
    - busybox
    - vi
    - adb
    - android
---

# 需求

## 原始需求
Android应用测试的时候经常需要修改/system/etc/hosts，有这么几种常见方法：  


* 1、 adb pull 拉取到PC上进行修改，修改后再 adb push 回手机（麻烦）
* 2、 直接用手机上的Root Explorer等工具进行编辑（默认的文本编辑很难操作）
* 3、 PC上直接使用adb shell vi命令（android系统默认是没有vi命令的）

## 落地需求
adb shell vi这个方案无疑是操作起来最高效的，那怎样才能可以在adb shell下使用vi命令呢？

# 操作步骤

## 0. 前提：你的手机已经ROOT过了

## 1. 手机上下载并安装BusyBox
* 我用的是这个：[BusyBox工具箱 汉化版](http://www.wandoujia.com/apps/stericson.busybox.donate)
* 我安装的版本是1.22.1
* 安装的时候直接安装到/system/bin，这样敲命令行的时候就不需要这么敲了：
> busybox vi

## 2. adb shell vi

### 2.1 非Windows机器直接在终端上adb shell就可以了，无缝使用

### 2.2 Windows下的 adb shell vi
* Windows下直接使用CDM连接后使用vi会出现乱码，解决的方案是使用：adbputty
* 从这里下载：[adb putty](https://github.com/sztupy/adbputty/downloads)
* putty配置：主机填写transport-usb ，连接类型选择Adb（对应5037端口）  

配置可以参考这个：[adb putty](http://yesokay.herokuapp.com/2012-07-31-adbputty.html)

## 3. vi 保存文件时提示：Read-only file system.
都是mount R/W的问题，同一个问题两个解决方案：
### 3.1 mount R/W
```
Simply change ro to rw and add the remount option
# mount -o rw,remount /system

Once you are done making changes, you should remount with the original readonly.
# mount -o ro,remount /system
```

### 3.2 还有个比较鸡肋，但是偷懒的解决方案
不记得命令行的时候可以使用下 :)

1. 使用Root Exporler等工具，点击Mount R/W按钮
2. 回到adb shell下操作，你会发现已经可以保存了