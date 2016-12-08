---
title: 'Hello Ruby on Windows'
layout: post
tags:
    - ruby
---

有人说在Windows上Ruby是件很虐心的事情，这段时间实践了下，其实一点也不然，有了RubyInstaller，纯Ruby的学习找就是无障碍一步到位了。

**当然闹心的事情还是有的 :)**  

### 1、如果你需要使用gem，那么真正虐心的事情来了：天朝的网络问题。
**解决方案：**  

<http://ruby.taobao.org/>    

按照首页上的指引操作就好了

### 2、公司代理问题（公司网络只能通过http代理来上网）  

gem也是可以设置代理的，不过有个简单的方法是给系统变量添加一个http_proxy，这样在CMD中运行gem   install就能直接使用代理来安装了 :)  

```
set http_proxy=<http://proxy.yourdomain.com:8080>  

```

### 3、给IDE增加Debug功能
你需要执行下面这个命令：  

> gem install ruby-debug-base19x

如果你发现失败了？而且错误提示中提到了“DevKit”，那么你需要先安装、配置好DevKit。  

从[<http://rubyinstaller.org/downloads/>   这里]下载，解压，到解压出来的目录下执行：  

> ruby dk.rb init
> ruby dk.rb review
> ruby dk.rb install
搞定，DevKit就安装好了，可以再执行下这个命令行，运气不是很差的话你，你已经可以在IDE中Debug Ruby了
> gem install ruby-debug-base19x

这里其实还有个更简便的方法：使用[RailsInstaller](http://railsinstaller.org/)

### 参考：
[解决Rubygems被墙，GEM无法更新](http://www.cnblogs.com/varlxj/archive/2011/10/16/2211004.html)  

[CMD 和 Git 中的代理设置](http://44ux.com/blog/2012/10/13/proxy-setting-in-cmd/)  

[Windows下安装DevKit](http://rubyer.me/blog/134/)  


RubyInstaller和DevKit都可以从这里下载到：http://rubyinstaller.org/downloads/