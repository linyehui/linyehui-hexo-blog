---
title: '关于CreateMutex的bInitialOwner参数'
layout: post
tags:
    - createmutex
    - windowsapi
---

# 我遇到的问题
两个进程，用Mutex进行互斥，后起来的进程WaitForSingleObject这个Mutex来等待前一个进程退出，期望是第二个进程可以Wait时卡住，但事实是没有，不过OpenMutex却可以拿到这个Mutex。

检查代码后发现和CreateMutex的第二个参数有关系，如果设置成TRUE就可以了。

不是很明白其中的道理，于是仔细阅读了MSDN上关于这个参数的说明，得到了一些启示。

## MSDN上的参数说明：
```
bInitialOwner [in]
If this value is TRUE and the caller created the mutex, the calling thread obtains initial ownership of 
the mutex object. Otherwise, the calling thread does not obtain ownership of the mutex. To determine 
if the caller created the mutex, see the Return Values section.
```

看完之后还不是很明白这个参数的具体意义，于是接着往下看，这段就说的比较清楚了：

```
The state of a mutex object is signaled when it is not owned by any thread. The creating thread can use 
the bInitialOwner flag to request immediate ownership of the mutex. Otherwise, a thread must use one of 
the wait functions to request ownership. When the mutex's state is signaled, one waiting thread is granted ownership, the mutex's state changes to nonsignaled, and the wait function returns. Only one thread can own a mutex at any given time. The owning thread uses the ReleaseMutex function to release its ownership.

The thread that owns a mutex can specify the same mutex in repeated wait function calls without blocking 
its execution. Typically, you would not wait repeatedly for the same mutex, but this mechanism prevents a thread from deadlocking itself while waiting for a mutex that it already owns. However, to release its ownership, the thread must call ReleaseMutex once for each time that the mutex satisfied a wait.
```

### 英文不好，第一次看是没太留意，后面发现了一个bug后才知道自己用错了。
1. 如果bInitialOwner参数是FALSE的话那么默认是signaled，也就是谁都可以Wait到（比如有第二个进程WaitForSingleObject了这个Mutex，那么会直接过去）
2. 如果不想第二个进程Wait时直接过去，有两个方法：
2.1 bInitialOwner设置为TRUE
2.2 bInitialOwner设置为FALSE，第一个进程在CreateMutex后主动WaitForSingleObject一次，我是这么干的，有用

> WaitForSingleObject(hMutex, 0);

### 两个的区别是什么：

```
Two or more processes can call CreateMutex to create the same named mutex. The first process actually 
creates the mutex, and subsequent processes with sufficient access rights simply open a handle to the 
existing mutex. This enables multiple processes to get handles of the same mutex, while relieving the user of the responsibility of ensuring that the creating process is started first. When using this technique, you should set the bInitialOwner flag to FALSE; otherwise, it can be difficult to be certain which process has initial ownership.
```


## 我最终使用的解决方法是2.2的方法：

 > bInitialOwner设置为FALSE，第一个进程在CreateMutex后主动WaitForSingleObject一次，我是这么干的，有用


# 参考：
[MSDN上的CreateMutex function](http://msdn.microsoft.com/en-us/library/windows/desktop/ms682411.aspx)