---
title: 'NSArray sortedArrayUsingFunction引发的内存泄漏'
layout: post
tags:
    - nsarray
    - ios
    - memory-leak
---

## 问题描述
对一个NSMutableArray进行排序，却造成了内存泄漏……

#### 代码说明：
在多线程的流程中，存在一个NSMutableArray *sortList；
对sortList进行排序，一开始用的方法是这样的，有内存泄漏：
```
NSArray *sortedSendQuery = [self.sortList sortedArrayUsingFunction:frameDataCompare context:NULL];
[self.sortList removeAllObjects];
[self.sortList addObjectsFromArray:sortedSendQuery];
```

修正后的代码只有一行，没有内存泄漏：
```
[self.sortList sortUsingFunction:frameDataCompare context:nil];
```

## 原因分析
NSArray的排序方法是：sortedArrayUsingFunction

NSMutableArray的排序方法是：sortUsingFunction

self.sortList是一个NSMutableArray，直接使用NSMutableArray的排序方法sortUsingFunction不需要进行内存复制，可以提高性能；

除了性能问题，由于在多线程中使用，而且如果NSMutableArray中的object内存分配来自于其他线程的，那么不必要的内存复制会导致内存泄漏，出现的内存泄漏问题类似于：

[Memory leaks from Multidimensional array: NSMutableArray, NSArray, addObject and insertObjectAtIndex](http://stackoverflow.com/questions/5200857/memory-leaks-from-multidimensional-array-nsmutablearray-nsarray-addobject-and)

## 结论
对于NSMutable的使用，要留意调用正确的方法，能不做类型转换就不做
