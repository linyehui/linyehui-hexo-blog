---
title: 'ignore SIGPIPE crash'
layout: post
tags:
    - ios
    - SIGPIPE    
---

## 问题
由于使用了Multipeer Connectivity和NSOutputStream，在某些网络环境切换的情况下，总是会遇到这样的crash：
> Signal 13 was raised. SIGPIPE

查找资料后，发现最简单的解决方案就是忽略这个错误：
> signal(SIGPIPE, SIG_IGN);

但实际的结果是我们设置了还是会继续出现闪退

## 原因
排查了很久后才发现，问题的原因是BugTags也会控制这个开头，默认是不忽略，而且这个默认显然代码是主动调用了，而不是我们期望的不调用就不设置：
```
/**
 * 是否忽略 PIPE Signal (SIGPIPE) 闪退，默认 NO
 */
@property(nonatomic, assign) BOOL ignorePIPESignalCrash;
```

## 解决方案
设置BugTags的对应开关即可：
```
// Bugtags
BugtagsOptions *options = [[BugtagsOptions alloc] init];
options.ignorePIPESignalCrash = YES;
[Bugtags startWithAppKey:BugTagsAPPKEY invocationEvent:BTGInvocationEventNone options:options];
```

## 思考
这个问题我们排查了很久，一直在找我们自己的原因，但其实这种bug因为跟网络环境有关系不容易重现；
其实反思后发现，排查这个问题我们应该先做一个事情，就是手动出发SIGNAL 13，看闪退是否被正确忽略；
而我们恰恰没做这个事情，思路上的疏漏导致了之前的大量工作变成了无用功。

## 参考材料
[如何在 iOS 上避免 SIGPIPE 信号导致的 crash (Avoiding SIGPIPE signal crash in iOS)](http://www.jianshu.com/p/1957d2b18d2c)
[NSStream close](https://developer.apple.com/reference/foundation/nsstream/1410399-close?language=objc)
[Writing OutputStreams](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Streams/Articles/WritingOutputStreams.html)
[[深入浅出Cocoa]iOS网络编程之NSStream](http://www.cnblogs.com/kesalin/archive/2013/04/29/ios_network_nsstream.html)
[How to make a NSStream instance reusable?](http://iphonedevsdk.com/forum/iphone-sdk-development/21065-how-to-make-a-nsstream-instance-reusable.html)