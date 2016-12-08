---
title: 'libc++和libstdc++'
layout: post
tags:
    - c++
    - mac
---

# 需求
cocos2dx 3.0之后的版本从对于标准库的使用从原来的GNU版libstdc++改成了LLVM的libc++，导致了我们需要用到的一部分第三方库.a文件无法使用了。

# 解决方案
因为这两个库的API接口是一致的，所以理论上不存在语法使用上的差异，你只需要修改下编译选项，重编下对应的第三方库即可。

### 举例说明

#### 开源库
boost，QT等都提供了指引
#### 第三方SDK
国内像友盟提供的SDK已经分别对应两个标准库提供了.a文件用于适配
#### 自己的库
修改下编译选项，重编下

# 两者的区别

### 简单描述

[Using libstdc++ compiled libraries with clang++ -stdlib=libc++](http://stackoverflow.com/questions/12542971/using-libstdc-compiled-libraries-with-clang-stdlib-libc)

```
Although they are API compatible, they are not ABI compatible. 
That means that if you construct a std::string with libstdc++, 
and then pass it to other code that is linked against libc++, 
the receiving code would think it has a libc++ std::string.
```

### 详细描述
[Defining a Portable C++ ABI - Open Standards](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4028.pdf)

# 为什么要有两个库？

#### GPLv3
GCC 4.2.1之后的版本License改成了GPLv3，这个许可协议和Apple在AppStore里使用的DRM技术不相容，也就是说Apple以及第三方开发者不能使用GCC4.2.1之后的版本生成AppStore应用，libstdc++其实一直是GCC工具集的一部分，所以只好停留在GCC 4.2.1自带的那个版本。

#### GCC对Objective-C新特性的支持很有限
GCC4.2.1及对应的libstdc++太老旧，不支持新的C和C++标准，也不支持Apple加入到Objective-C里的新特性比如Block、ARC等。

[知乎：Apple 为什么在 Mavericks 里把 C++ 标准库从 libstdc++ 改成 libc++？](http://www.zhihu.com/question/23507141)

# 一件小事
由于我自己的疏忽，我的GoDaddy账号被盗了，上面的6个域名(包括linyehui.com)被小偷转移，目前还在申诉中。

虽然事情还没结束，但处理的过程让我对GoDaddy非常失望，以后绝不会在他们家注册任何服务：GoDaddy对你的财产安全根本不在乎。

```
1.如果小偷拥有了你的密码，小偷做了任何事情GoDaddy都不认账;

2.稍微有点安全意识的网站，改个密码都要短信或者邮件验证下，
而GoDaddy转移货真价实的财产（域名）竟然连个验证邮件都没有；

3.域名被转移后，GoDaddy只会事后发一封知会邮件，而且虽然邮件上面说15天之内可以undo，
但是所谓的undo只是一句空话，还是要走非常无力的申诉流程。

```

### 后续
万一域名要不回来，这也不是什么大事情，关注博客的同学还可以继续从linyehui.github.com访问。

要加强防范，做好密码分级，并且定期修改密码。

这一切没有想象的那么糟，不是吗 :)