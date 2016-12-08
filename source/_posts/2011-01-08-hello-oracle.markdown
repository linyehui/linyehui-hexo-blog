---
title: 'Oracle初体验'
layout: post
tags:
    - hello
    - oracle
---

由于某种不可抗拒的力量，我必须把一个Oracle XXX系统跑起来，按正常来说只是跑起来这应该很简单，又不需要自己写一个，但是我在Oracle上确实遇到了麻烦，这东西很不人性化，是我接触的数据库中最难上手的一个。

# 1、安装问题
我的机器安装的是Win7，在Oracle官网下载了最不同的10g Express版本，安装过程和使用过程中一堆莫名其妙的错误，后面试试感觉应该也问题不大，但整个流程让人很不放心无法放心使用。在我的一番尝试下（10g Express, 10g 企业版，10g for vista, 11g），结论是：Win7下还是10g for vista版本最靠谱。

# 2、添加用户
新建数据库后，Oracle会在开始菜单为你提供一个管理网页的链接，我原本以为这个网页应该会累死phpAdmin那样方便，结果大失所望，一堆功能的麻木堆砌，不是一般的难用。

最后我还是采用了SQL Plus的命令行方式进行添加用户，找到命令行一下子找回了数据库的感觉。

# 3、导入数据
Oracle的导入导出数据是有专门的命令：exp和imp，需要注意的是这两个命令不是在SQL Plus下使用，而是在普通的CMD命令行下执行，具体命令行使用可以参考这个:  

[Oracle exp/imp导出导入工具的使用](http://www.blogjava.net/lyyb2001/archive/2007/09/03/142172.html)。

## 简单描述下我的导入数据步骤，因为我在这地方卡住了，╭(╯^╰)╮

### 001、为要导入的数据专门新建一个用户，在SQL Plus下进行（我新建了个用户:linyehui 密码:password）：

```
--创建用户
create user linyehui identified by password
default tablespace users
temporary tablespace temp
quota 10m on users;

grant create session,resource to linyehui;
```

### 002、在cmd命令行下：

> $ imp linyehui/password file = all.dmp full=y


你应该可以得到类似的提示，如果你的数据是正确的话：

```
    Import: Release 10.2.0.3.0 - Production on 星期六 1月 8 14:30:40 2011
    

    Copyright (c) 1982, 2005, Oracle.  All rights reserved.
    

    

    连接到: Oracle Database 10g Enterprise Edition Release 10.2.0.3.0 - Production
    With the Partitioning, OLAP and Data Mining options
    

    经由常规路径由 EXPORT:V09.02.00 创建的导出文件
    已经完成 ZHS16GBK 字符集和 AL16UTF16 NCHAR 字符集中的导入
    . 正在将 ************************
    . . 正在导入表                          "TABLE1"导入了           4 行
    . . 正在导入表                        "TABLE2"导入了           7 行
    . . 正在导入表                         "TABLE3"导入了          12 行
    成功终止导入, 没有出现警告。
```


# 结语
好了，数据库方面的问题已经搞定了，剩下的就是调用的问题了，<http://twitter.com/#!/linyehui/status/23642782056841216> Y(^o^)Y