---
title: '修改CleareCase的默认文件比较工具'
layout: post
tags:
    - clearcase
    - difftool
    - mergetool
---

ClearCase本身就是个古老的东西，他自带的Diff工具更是难用的要死，所以我不得不想办法给他替换掉，改成我自己惯用的BeyondCompare。

在CC的界面上找了好久，就是找不到修改比较工具的位置，CC你能再难用点吗？！！  

于是我下意识的想到：有没有配置文件可以修改？

用ClearCase Check in是能够发现他的Diff工具是个独立进程，于是我就用ProceXP查看了进程路径，在ClearCase的目录下用TotalCommand进行文字搜索，一下在就找到他的配置文件：  

> C:\Program Files\IBM\RationalSDLC\ClearCase\lib\mgrs\map

修改这个配置文件，重新再ClearCase Check in窗口双击，哈哈，弹出来的比较工具已经变成了BeyondCompare