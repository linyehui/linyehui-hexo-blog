---
title: '在Mac下使用Beyond Compare'
layout: post
tags:
    - beyond-compare
    - config
    - git
    - mac
    - sourcetree
---



### 需求描述

* Windows下的Beyond Compare是个神器，Mac下也有，那么怎么在Mac下使用Beyond Compare呢？
* 本文所使用的Beyond Compare是官网下载的Beta版本，版本号：Version 4.0 beta (build 17905)，安装包我是从[官网](http://www.scootersoftware.com/support.php?zz=kb_mac)下载到的。

### Mac下命令行使用Beyond Compare

Mac下的Beyond Compare 4，如果要使用命令行，需要从Beyond Compare的菜单上先安装下命令行工具：
> Beyond Compare -> Install Command Line Tools ...
安装之后你就能得到：
> /usr/local/bin/bcomp:
> Launches comparison and waits for it to complete.
> 
> /usr/local/bin/bcompare:
> Launches comparison and returns immediately.
参考：[Using BC4 OSX with Version Control Systems](http://www.scootersoftware.com/vbulletin/showthread.php?t=11677&highlight=sourcetree)

### 在git中使用Beyond Compare

直接修改.gitconfig就可以了
> [diff]
>     tool = bcomp
> [difftool]
>     prompt = false
> [difftool "bcomp"]
>     trustExitCode = true
>     cmd = "/usr/local/bin/bcomp" "$LOCAL" "$REMOTE"
> [merge]
>     tool = bcomp
> [mergetool]
>     prompt = false
> [mergetool "bcomp"]
>     trustExitCode = true
>     cmd = "/usr/local/bin/bcomp" "$LOCAL" "$REMOTE" "$BASE" "$MERGED"

参考：[Using BC4 OSX with Version Control Systems](http://www.scootersoftware.com/vbulletin/showthread.php?t=11677&highlight=sourcetree)

### 在SourceTree中配置Beyond Compare

1.首先你要确认你已经从Beyond Compare菜单上安装了命令行工具
2.在SourceTree的Preferences中配置
> Visual Diff Tool: Other
> Diff Command:/usr/local/bin/bcomp
> Parameters:$LOCAL $REMOTE
> Merge Tool: Other
> Merge Command:/usr/local/bin/bcomp
> Paramters:$LOCAL $REMOTE $BASE $MERGED

参考：[SourceTree configuration](http://www.scootersoftware.com/vbulletin/showthread.php?t=11695&highlight=sourcetree)

------

喜欢Beyond Compare的话就支持正版吧 :)