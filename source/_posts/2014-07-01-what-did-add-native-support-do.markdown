---
title: 'Add Native Support 都做了些什么'
layout: post
tags:
    - add-native-support
    - android
    - ndk
---

### 问题

> Android工程不小心点击了Android Tools->Add Native Support，怎么还原

### 还原步骤

#### 1. 删除工程根目录的 .cproject文件

#### 2. 删除.project中的cdt部分

* Add Native Support后的工程会在<buildSpec>下多出一个<buildCommand></buildCommand>XML段，找到包含cdt的buildCommand XML段删除
* Add Native Support后的工程会在<natures>下多出几个包含cdt的XML段，删除

#### 3. 回到Eclipse，刷新，右键工程->Android Tools->Add Native Support又回来了


#不折腾不舒服斯基

-----The End-----