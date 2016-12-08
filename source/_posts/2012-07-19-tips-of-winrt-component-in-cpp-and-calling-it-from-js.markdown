---
title: 'WinRT下JavaScript调用C++ WinRT组件的注意事项'
layout: post
tags:
    - component
    - javascript
    - tips
    - winrt
---

有段时间没有写WinRT相关的文章了，这段时间还会继续，因为我们目前在进行界面应用层的HTML5化 :)

# 今天要和大家分享的是：

WinRT下，使用HTML+JavaScript实现的界面应用层，调用C++实现的底层WinRT组件（Windows Runtime component）的相关注意事项


# 嗯，标题很长，下面我们来干货：
* 0、提供给JS工程调用的C++类必须是ref类，这个好像不用说吧？:)
* 1、JS调用的函数名首字母必须是小写，无论你C++代码实际是不是小写，比如：KillBill()，在JS中调用的时候必须写成killBill()
* 2、JS调用的delegate方法，在赋值时要注意加上前缀"on"，同时全部字母小写，比如：C++类中有这么一个成员变量：
> event OOXXEventDelegate^ OOXXEvent，
那么你在JS中给他赋值时必须这么写：
> objOOXX.onooxxevent = handler_ooxx;
* 3、提供给JS工程调用的C++类的命名空间必须和DLL的名字相同，比如你的DLL叫做SuperEngine，那么你的类的顶级命名空间也必须是SuperEngine
* 4、JS可以调用ref 类的静态方法，但是记得方法名一样要首字母小写
* 5、回调回JS的代码必须在主界面线程中回调，否则会失败，这个在XAML下也是一样的，解决的方法还是比较简单的，就是在一开始保存下当前的Dispatcher，
> auto dispatcher = Windows::ApplicationModel::Core::CoreApplication::GetCurrentView()->CoreWindow->Dispatcher;
然后回调的地方用RunAsync跳转下：
> auto callback = ref new Windows::UI::Core::DispatchedHandler([=](){
> m_event->OOXXEvent(eCode, ref new Platform::String(strErrTip.pstr));
> });
> m_dispatcher->RunAsync(Windows::UI::Core::CoreDispatcherPriority::Normal, callback);

* 6、导出的类必须要有CPP文件JS那边才能访问的到
* 7、导出的类所继承的类不可以包含数据成员变量和Event类型的成员变量，private的也不行
* 8、导出的类可以继承接口，接口中可以包含event类型的变量(理论上其实event就是个函数），这条和上一条并不冲突。
举个例子说明下这两点：

	* 8.1、你有一个接口INotifyPropertyChanged，接口只有一个方法：event PropertyChangedEventHandler^ PropertyChanged;
	* 8.2、如果：你的实现类直接继承了INotifyPropertyChanged，那么这样的导出类是没有问题的（这个时候按照语法你需要在你的实现类中这样声明下：  

     virtual event PropertyChangedEventHandler^ PropertyChanged;

	* 8.3、如果：你的实现类，继承了一个类叫做NotifyPropertyChangedBase，而NotifyPropertyChangedBase又继承了INotifyPropertyChanged，那么这个代码在C++这边编译没有问题，但是JS一调用就会crash：  

	An unhandled win32 exception occurred in WWAHost.exe

* 9、还有什么回头想到我再补上

# 微软官方提供的例子其实还是挺不错的，只是说的没那么清楚：
[http://msdn.microsoft.com/en-us/library/windows/apps/hh755833(v=vs.110).aspx MSDN：Walkthrough: Creating a basic Windows Runtime component in C++ and calling it from JavaScript]