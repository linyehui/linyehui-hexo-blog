---
title: '无法访问“我的图片(Pictures Library)”中的文件'
layout: post
tags:
    - access
    - pictures-library
    - win8
    - winrt
---

# 问题
在WinRT下，文件访问有着严格的限制，默认你只能访问**Windows::Storage::ApplicationData::Current**  下属的一些目录，连“我的文档”，“我的图片”都无法访问。  

那么如果我们想从“我的图片”中选择一张图片并上传，我们应该怎么做呢？

我当时也找到了这个[MSDN帖子:Access denied on Pictures library](http://social.msdn.microsoft.com/Forums/en-US/winappswithcsharp/thread/0fef7363-73c6-4ff5-8e03-14b795e47068)，但没有得到答案，最终我自己解决后回复如下：

> If you want access a file in Document Library，you must follow: 
1.select "Document Library Access" in "Capabilities" Tab of ".appxmanifest" 
2.add "File Type Associations" in Declarations Tab of ".appxmanifest" 
> 
you can get sample from "File access sample", which is one of "Windows Developer Preview Metro style app samples"

# 描述下我的解决方案：
* 1、打开.appxmanifest文件，进入"Capabilities" Tab页，将"Picture Library Access"勾上
* 2、打开.appxmanifest文件，进入"Declarations" Tab页，添加"File Type Associations"，并在右侧的**"Supported File Types"**中加上“.png”、".jpg"等的文件类型支持。  

这一步我自己也是后面自己查看后才发现的，一定要特别留意。

* 3、有了上面两步，你就可以用Windows::Storage下的API进行访问和修改Picture Library下的文件了，不过你还是不能用Win32的API来访问下面的文件，怎么办呢？

### 方法还是有的：
用Windows::Storage下的CopyAsync方法先把文件复制到应用自己的目录下：Windows::Storage::ApplicationData::Current->TemporaryFolder，  

在这个目录下的文件你就可以用CreateFile2、fopen等方法来访问了。  


从Picture Library复制到App Temp目录下的代码就暂时不放出来了，除非有人需要，其实就是一堆的Lambda表达式

# 小结：
其实你在“Capabilities”中选择“Document Library Access”时，右边的文字提示对这个问题说的很清楚，只是大家一开始都没太留意，原来还有一个步骤要做：  

> The package can onle access file types in the documents library that are defined in the manifest 
using the File Association Handler declaration.

原文：

> Proviedes the capability to add, change, or delete files in the documents library. The package can onle access file 
types in the documents library that are defined in the manifest using the File Association Handler declaration.
The package cannot access document libraryies on HomeGroup computers.