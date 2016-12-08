---
title: 'WinRT下软键盘导致的“黑屏”'
layout: post
tags:
    - inputpane
    - softkeyboard
    - winrt
---

在WinRT下使用软键盘可能会出现“黑屏”的Bug，这个时候你需要特别留意下是不是使用错了InputPaneVisibilityEventArgs下面这个属性：

```
EnsuredFocusedElementInView
Read/write	Identifies whether the application has taken steps to ensure that the input pane doesn't cover the UI element that has focus.
```

出现黑屏的问题，试试在InputPane的Showing事件处理时将EnsuredFocusedElementInView赋值为false

```
The InputPane class has the following events. 
Hiding    Occurs when the input pane is about to be hidden by sliding out of view.
Showing   Occurs when the input pane is about to be displayed by sliding into view.
```

两个相关的MSDN链接：  

[InputPane class](http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.viewmanagement.inputpane.aspx)  

[InputPaneVisibilityEventArgs class](http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.viewmanagement.inputpanevisibilityeventargs)