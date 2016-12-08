---
title: '升级DuiEngine到2013'
layout: post
tags:
    - duiengine
    - ui
    - wtl
---

在我开始接触之前，有个很早的项目，使用的是DuiEngine一开始的开源代码，具体时间和版本已经找不清楚了，然后呢现在我需要把他升级到最新版本的[DuiEngine](http://duiengine.googlecode.com/)，在从老的库升级到新版本时遇到了下面这些问题。

### DuiEngine版本


    http://duiengine.googlecode.com/svn/trunk
    Revision: 246
    Author: setoutsoft@qq.com
    Date: 2013年8月26日,星期一 14:23:51
    Message:
    修改窗口默认属性的实现，删除原来的baseClass


### 升级过程中遇到的问题及解决方案

#### 1.skin xml描述类的变化

1.1 原来winres.h中的资源ID是手动一个个添加的，新版本的DuiEngine已经可以用工具自动生成  

解决方案：  

删除之前生成的winrex.h和name2id.xml，用命令行重新生成，其实就是给每个资源在xml中添加一个name属性，体力活，耐心改下，有个简单的方案是把你之前在winres.h中定义的ID作为xml中的name属性，比如：IDC_TXT_TITLE
> ..\3rdparty\duiengine\tool\residbuilder -y -p skin -i skin\index.xml -r .\duires\winres.rc2 -n .\duires\name2id.xml -h .\duires\winres.h 


1.2 模板生成的索引xml文件名字从idmap.xml改成了index.xml，这个只是名字的区别，DuiEngine的Demo目录结构还是老的，没有影响

1.3 resid type="PNG"被resid type="IMGX"替代，直接查找替换即可

1.4 header=“1”属性被去掉了，改成新的标签了，具体使用方法可以在DuiEngine Demo中搜索<caption>
解决方案：删除header="1"属性，改成用<caption></caption>

1.5 如果对应的xml文件是布局类型的xml，那么记得在index.xml中加个属性layer="1"

```
<resid type="XML"  id_name="IDR_MAIN_DIALOG" id="103" layer="1" file="xml\dlg_main.xml"  />
```

1.6 duiedit改名成了edit，需要在xml添加几个配置，照着Demo工程COPY就好了

1.7 imgframe替换了原来的imghorzex,png等skin类型

#### 2.编译中对ATL的依赖

我个人比较喜欢WTL，但是新版本中用WTL.mini进行了替换，好在可以用USING_ATL预编译宏进行开关选择继续使用WTL，使用的时候DuiEngine和使用的工程都需要使用这个预编译宏，否则会出现link error

#### 3.static lib releaes编译时的诡异link error

最后我发现了问题的所在，是一个编译选项配错了，改成默认的就可以了  

C/C++->Language->Treat wchar_t as Built-in Type  

改成默认的Yes就可以link成功，编译通过了，我不知道是不是我自己不小心改出来的，不过好像google code取下来的代码就是有这个问题的

### 小结

昨天下午开始做这个事情，工程我已经迁移完成了，改动的过程比较痛苦，但DuiEngine整个UI库的设计理念没有太大的变化，所以都是些细节，作者也比较懒，没有一个升级的指引，源代码就是文档 :(  

新版本还是改了很多Bug的，我比较奇怪的是作者为什么要把WTL给去掉呢？自己实现的SimpleWnd功能还不全，合并呢？  


我本来可以沿用老版本的DuiEngine，但是本着开源代码就是要用最新的原则，我还是进行了版本升级，过程比现象中的要纠结，但时间比想象中花的少。  


Thanks DuiEngine, gl hf :)