---
title: 'type_half.inl file not found'
layout: post
tags:
    - cocoapods
    - ios
---

## 需求
通过CocoaPods使用VideoCore，出现编译错误：
```
'type_half.inl' file not found
```

## 解决方案
I solved this problem by change the search path setting in Pods project. Just remove all ""${PODS_ROOT}/Headers/Private" in “Header Search Paths"

记得选着Pod工程下的VideoCore这个Target进行配置，这个不是最优的方案，但是算是比较简单的方案。

## 参考文档
[GLM Include Error #209](https://github.com/jgh-/VideoCore/issues/209)
