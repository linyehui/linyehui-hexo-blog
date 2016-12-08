---
title: 'WinRT本地化注意事项'
layout: post
tags:
    - localization
    - multilanguage
    - winrt
---

# Win8下的多语言实现，可以参考这几个说明：
[Localizing the package manifest](http://msdn.microsoft.com/en-us/library/windows/apps/hh454044(v=vs.110).aspx)
[QuickStart: Make your Metro style app world ready](http://msdn.microsoft.com/zh-cn/library/windows/apps/xaml/hh771181.aspx)


# 修改工程的默认语言
其中有一个默认语言的说法，这个在哪里设置呢？  

用记事本打开工程的vcxproj文件，搜索：  

```
<DefaultLanguage>zh-CN</DefaultLanguage>
```

对的，修改这个就可以修改你的应用的默认语言了

# 多语言时在.resw文件中使用\n等特殊字符
1. 直接在VS2011中打开.resw文件的话使用VS的编辑器打开，在其中配置了字符串如"\n“后你会发现，展示在界面上时变成了”\\n“！
2. 坑爹吧，于是你可能想到应该把"\n"用转义符："&#13;"替换，嗯，不错的思路，不过还是错的，因为这个时候实际会变成："&amp;#13;"！
3. 还是坑爹啊，告诉你答案吧：不要用VS的编辑器打开，用文本格式打开.resw文件，手动添加"&#13;"就可以了

# 帮助我解决这个问题的参考文章：  

[WPF和字符串格式化](http://www.zhcnco.info/index.php?db=so&id=115431)

回头发现，其实你需要回车的话可以直接在VS的编辑器中按：Shift+Enter