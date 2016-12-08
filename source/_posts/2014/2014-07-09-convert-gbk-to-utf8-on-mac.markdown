---
title: 'Xcode下的中文乱码问题'
layout: post
tags:
    - xcode
    - ios
    - gbk
    - utf8
---


===========

# 问题原因
绝大部分的中文乱码是因为文件编码不对导致的：

1. 使用了GBK的文件编码方式在Xcode下是无法识别的
2. 老版本的Xcode对带BOOM头的UTF8文件编码也是无法识别的（我的Xcode 5.0.2已经支持带BOM头的UTF8文件了）

出现上述情况一般都是因为跨平台共用文件导致的，Windows，Mac共同使用的代码文件会出现乱码的问题。

# 最好的解决方案
Windows和Mac下统一使用UTF8带BOM头的文件编码格式。

### 特别解释下

#### 以前是这样的：
UTF8不带BOM头的文件，在xcode和eclipse下显示正常，但Windows下会中文乱码；  

UTF8带BOM头的文件，Windows下显示正常，但Xcode和eclipse下会中文乱码；

#### 现在是这样的：
Xcode 5.0.2和ADT 22.3.0都已经支持显示带BOM头的UTF8格式。



# Mac下遇到这种乱码怎么解决

## 方法一：使用Sublime的解决办法
Sublime Text 2下，使用Package Control 安装 ConvertToUTF8 这个插件：

1. 使用Sublime打开乱码文件，正常是可以自动识别出来是GBK还是GB2312等编码的
2. 如果识别不出来，那么就手动选择下菜单File-》Reload With Encoding，选择GBK，应该也能正确显示中文了
3. 正确显示中文后，就可以把文件保存成UTF8格式了，但是直接Save是不行的，保存之前必须先做一件事情：
> File -> Set File Encoding to -> UTF8
4. 保存文件

## 方法二：Xcode的File inspector属性窗口
1. 在工程中打开乱码的文件，选择File inspector属性窗口
2. 选择Text Encoding，比如我选择“GB 2313”（不对的话可以换着试试看）
3. 弹出提示，选择“Reinterpret”，如果中文显示正确，就下一步，否则就再换个编码试试

    > Do you want to convert the text to 'Simplified Chinese (GB 2312)'?
    >
    Choose 'Convert' if you want to change the contents of the file to be encoded as 'Simplified Chinese (GB 2312)'.
    > 
    Choose 'Reinterpret' if you believe the file has been opened with an incorrect encoding and you want to reopen it as 'Simplified Chinese (GB 2312)'.

4. 把成功显示中文后的文件保存成UTF8格式：  

再选择下Text Encoing->UTF8，这次出现的提示选择Convert

5. 搞定