---
title: '给Xcode5下Empty Application模板创建的工程添加Interface Builder'
layout: post
tags:
    - ios
    - xcode
---

### 1. 新建一个Empty Application的工程

Xcode->New Project->Empty Application

### 2. 填上工程的名字，比如HelloWorld，其他的保持默认，然后点击“Next”


### 3. 选择工程保存的路径，然后点击“Create”


### 4. File->New File->左边的导航选择Cocoa Touch->右边选择Objective-C Class->点击“Next”


### 5. 配置下类的信息如下：

Subclass of：UIViewController （这个先填，Xcode会帮你生成Class的值）
Class：HelloWorldUIViewController
Targeted for iPad:不勾选
With XIB for user interface:勾选
然后点击“Next”

### 6. 选择路径后，点击“Create”

你会发现工程下新增了三个文件：
HelloWorldUIViewController.h
HelloWorldUIViewController.m
HelloWorldUIViewController.xib

### 7. 把新建的UIViewController显示到界面上

7.1 打开AppDelegate.m文件，并在
> #import "AppDelegate.h"
之后加上一句
> #import "HelloWorldUIViewController.h"

7.2 在这句注释之后添加两行我们的代码
> // Override point for customization after application launch.
修改后的代码如下：

    // Override point for customization after application launch.
        HelloWorldViewController *hvc = [[HelloWorldViewController alloc] init];
        [[self window] setRootViewController:hvc];


### 8. 编译，运行


===The End===