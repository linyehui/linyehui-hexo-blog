---
title: 'Double Commander for Mac'
layout: post
tags:
    - mac
    - totalcommander
    - doublecommander
---

### 相见恨晚
作为一个深度依赖Total Commander的键盘党，切换开发环境到Mac后，一直找不到一款可以在Mac上接替TC的产品，试用过Path Finder、ForkLift都无法达到我的需求，不是因为界面的缘故，而是产品的设计理念根本就不一样。

TC确实可以替换系统的资源管理器，但是他的目标可不是替换系统资源管理器这么简单。

无奈之下选择了一款免费的Finder插件XtraFinder，用了一段时间之后，我的Mac升级了OS X 10.11，用不了了：[如果不关闭System Integrity Protection就无法使用了](https://www.trankynam.com/xtrafinder/sip.html)。

不能忍，再找找吧，在网上看到了这几个帖子：  

[怎么，Mac 下的诸多文件管理软件还在仰望 Total Commander，在 TC 的阴影下生活么？](https://www.v2ex.com/t/66737)  

[Mac 是否有类似Windows上 Total Commander 这样的应用？](http://www.zhihu.com/question/19607061)

几个帖子中都提到了一个我之前没有试用过的软件：[Double Commander](http://doublecmd.sourceforge.net/)

用了一分钟之后，我只能说：相见恨晚

## 小技巧

### Mac下的版本选择
```
Mac OS X >= 10.5
doublecmd-0.6.6-6327.carbon.i386.dmg

Mac OS X >= 10.8	
64 bit	doublecmd-0.6.6-6327.qt.x86_64.dmg
```

#### 结论是：选择32位版本

#### 理由： 
1、性能上的差别没有做比较，但是两者使用下来没有明显的差异，至少没有卡顿的情况；  

2、除去性能，64位的界面太毛糙了，无论是图标还是界面的文字显示效果都很差；  


两者的界面库是不同的，32位下采用carbon，而64位下使用的是Qt，carbon的界面是使用系统原生来适配的，而qt的界面是跨平台的。

举个简单的例子：carbon可以使用OS X 10.11后的新字体，而Qt没有使用。


### 快捷键冲突

#### 复制、剪切、和粘贴
为了方便在Finder和DC之间复制文件，对应Ctrl+C、Ctrl+X、Ctrl+V分别多加一个快捷键   

``` 
Command+C、Command+X、Command+V
```

#### 左右窗口的复制快捷键Ctrl+左/右和Mac的屏幕切换冲突了
我的做法是自定义快捷键：  

```
Ctrl+option+左
Ctrl+option+右
```

其他的快捷键冲突，也使用类似的解决方案


###  对键盘党来说必备的小功能
F5复制，F6移动  

快速过滤定位文件  

文件内查找文本  

批量重命名  

压缩文件  

比较文件  

等等……  


## 小结
Path Finder等收费软件虽然界面很美观，但产品的定位上只是Finder的一个增强或者替代品，和Total Commander没有可比性；  

作为一个独立的效率工具来说，Double Commander和Total Commander定位非常接近；  

另外还有一点，作为开源软件的Double Commander，他的版本计划还在继续，非常值得期待。  
