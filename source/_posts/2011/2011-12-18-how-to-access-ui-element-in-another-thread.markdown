---
title: 'WinRT下如何在另外一个线程中访问UI线程'
layout: post
tags:
    - async
    - winrt
---

# 我的问题是：
WinRT下如何在另外一个线程中访问UI线程 How To Access UI Element In Another Thread
在UI所属线程之外的线程调用UI元素的方法或者属性会导致异常，怎样解决这样的问题呢？

# 微软为我们提供了这样的方法：
> Windows::UI::Core::CoreDispatcher->Invoke

这个方法会将函数调用放到CoreDispatcher所属的线程中进行调用，而这个CoreDispatcher一般属于界面线程，可以这么得到：
> Window::Current->CoreWindows->Dispatcher

# 示例：
假设存在一个回调接口的实现类CXXCallback，专门用于接收别的线程的回调消息，然后负责通知界面更新

```cpp
void CXXCallback::OnUIChange()
{
   auto callback = ref new Windows::UI::Core::InvokedHandler([this](Platform::Object^ sender, Windows::UI::Core::InvokedHandlerArgs^ eInvoke){
   *** 进行界面更新，调用界面元素的方法**
   *** ……**
   },Platform::CallbackContext::Any);
   **m_coreDispatcher**->Invoke(Windows::UI::Core::CoreDispatcherPriority::Normal, callback, m_coreDispatcher, nullptr);
}
```

**m_coreDispatcher** 可以从任何CoreWindows的派生类型中获取到，可以想象到WinRT的API在底下帮我们做了什么事情？

## 我们猜测底层的实现机制可能类似：
1、一个窗口，用来PostMessage  

2、帮我们维护一些全局变量，让我们无缝的使用CXXCallback的类成员变量和OnUIChange()函数内的临时变量


# 比较有用的参考资料：
[Async everywhere: creating responsive APIs & apps](http://channel9.msdn.com/Events/BUILD/BUILD2011/PLAT-203T)PPT的第31页的位置  

4 Don’t worry about Dispatcher.Invoke…

[How to Access UI Element in a Thread Routine? ](http://social.msdn.microsoft.com/Forums/en/winappswithnativecode/thread/e36f84be-db95-4764-951e-bfa662cdd11d)