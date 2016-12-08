---
title: 'VisualSVN设置提交时必须输入log信息'
layout: post
tags:
---

# 需求描述
自己在Windows上用VisualSVN搭了个服务器，默认提交代码是可以不填任何信息，这可不是我所期望的，于是找到了下面的解决方案：

在VisualSVN的管理控制台中可以设置这些规则，这些规则在特定的时机触发，其中Commit代码之前检查是否填写了log信息，我们可以在pre-commit时进行。

# 设置方法：
1. 打开VisualSVN管理控制台
2. 右键你要设置的代码仓库
3. 所有任务-》Manage Hooks……
4. 编辑“Pre-commit hook"

其实这个编辑对应的是在你的代码仓库目录下的Hook子目录生成了一个.  cmd的文件，我们自己修改这个文件就可以了，不需要通过界面修改。

在hook目录有一些模板，但模板用的是linux的命令，把模板的后缀名修改成cmd并不能正确执行，所以我们需要符合Windows的Hook脚本，我测试了两个，一个是纯DOS命令，一个是需要perl，都测试OK，还有一个pytyon的版本我就没有测试了，具体可以参考我最后列出来的参考文档。

## 1、纯Windows DOS命令

**只需要一个文件就可以：**pre-commit.cmd

```cmd
    @echo off
    ::    
    :: Stops commits that have empty log messages.
    ::
    
    @echo off
    
    setlocal
    
    rem Subversion sends through the path to the repository and transaction id
    set REPOS=%1
    set TXN=%2
    
    rem check for an empty log message
    svnlook log %REPOS% -t %TXN% | findstr . > nul
    if %errorlevel% gtr 0 (goto err) else exit 0
    
    :err
    echo. 1>&2
    echo Your commit has been blocked because you didn't give any log message 1>&2
    echo Please write a log message describing the purpose of your changes and 1>&2
    echo then try committing again. -- Thank you 1>&2
    exit 1
```

需要注意的是svnlook这个是在VisualSVN安装目录下的bin下面，你可以用全路径来替换或者设置系统环境变量

## 2、CMD配合perl实现，还可以判断Log信息的最小长度，比较灵活，需要两个文件

pre-commit.cmd：

```
set REPOS=%1 
set REV=%2 
"C:\Perl\bin\perl.exe" "E:\Repositories\woodpecker\hooks\custom-pre-commit.pl" %REPOS% %REV%
```

custom-pre-commit.pl：

```
#!/usr/bin/perl

# config section
$minchars = 4;
$svnlook = '"D:\Program Files\VisualSVN Server\bin\svnlook.exe"';

#--------------------------------------------
$repos = $ARGV[0];
$txn = $ARGV[1];
$comment = `$svnlook log -t "$txn" "$repos"`;
chomp($comment);

if ( length($comment) == 0 ) {
  print STDERR "A comment is required!";
  exit(1);
  }
elsif ( length($comment) < $minchars ) {
  print STDERR "Comment must be at least $minchars characters.";
  exit(1);
  }

exit(0);
```

当然第二个是需要你按照perl的

## 第一个方便点，第二个灵活点

# 参考文献：
[Perl pre-commit hook](http://groups.google.com/group/visualsvn/browse_thread/thread/5d73e465218abe51)  

[Require subversion comments – a perl pre-commit hook](http://www.stillnetstudios.com/require-subversion-comments-minimum/)  

[VisualSVN pre-commit rule](http://stackoverflow.com/questions/2920189/visualsvn-pre-commit-rule)  

[VisualSVN 提交时必须输入消息设置](http://hi.baidu.com/djzbj/blog/item/60c5e9ea2c91f8d8d539c9bf.html)