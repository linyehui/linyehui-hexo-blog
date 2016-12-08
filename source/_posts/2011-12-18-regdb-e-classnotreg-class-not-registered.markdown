---
title: 'REGDB_E_CLASSNOTREG Class not registered'
layout: post
tags:
    - vs2011
    - winrt
---

# 一个诡异的问题：
WinRT多工程情况下，调用另一个DLL中的接口，构造那个类型的时候就会直接抛出异常，错误码为0x80040154，REGDB_E_CLASSNOTREG，注释是：Class not registered

# 最终在MSDN上找到相关的解答是：
[Creating Windows Runtime Components in C# and Visual Basic](http://msdn.microsoft.com/en-us/library/windows/apps/br230301.aspx)

> Visual Studio 11 Express for Windows Developer Preview fails to add classes to the app manifest if they don't have default constructors. 

>  This causes a "Class not registered" error at run time, when JavaScript code tries to instantiate the class. The workaround is to add a default constructor.

>  '''Important'''  

When you encounter this error, simply adding a default constructor to the class doesn't fix the problem. Stop the debugger and then delete the AppXManifest.xml file from the Debug folder, forcing Visual Studio to regenerate the file and add the class. Alternatively, you can manually add the class to the extensions section of the manifest.


## 简要来说：
就是AppXManifest.xml这个文件编译时生成的有问题，没有把DLL中的类生成完整，导致另外的工程调用时无法识别；
手工添加上后就可以成功调用了。