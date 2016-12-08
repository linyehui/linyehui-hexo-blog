---
title: 'Total Command导入文件选择列表'
layout: post
tags:
    - windows
    - total-command
---

## 需求
对SVN入库的文件进行PNG优化，我使用PNGoo进行PNG优化，我已经从SVN记录中得到了所有需要更新的PNG文件全路径，但是怎么把这些分散在不同目录下的文件加入PNGoo中是我想要解决的问题

## 方案一：Windows的文件选择框
PNGoo使用的Windows的文件选择框是支持文件多选的，支持的文件格式类似：  

```
"F:\test\Activity\100.png" "F:\test\ActivityItem\ActivityItem0.png"
```

文件路径中间以空格隔开即可。

但是这个方法再文件多了之后有问题：一次加入10个文件的话会只有前几个被加入进去，后面的你还得再一点点往里面加。  


显然，像我这么懒的人是不会喜欢这个方案的。  



## 方案二：Total Command导入文件选择列表

### 纯手动的解决方案
1. Total Command，到资源目录下Ctrl+B，把文件展开
2. 根据文件名，快速定位文件，然后空格选择这个文件
3. 按ESC返回文件过滤，重复步骤2，知道所有文件都已经被选中
4. 拖动Total Command选中的文件列表到PNGoo中去
5. 完成

显然，像我这么懒的人还是不会喜欢这个方案的，但是这个方案却有改进的空间。

### 半自动的解决方案
1. Total Command，到资源目录下Ctrl+B，把文件展开
2. 把准备好的文件选中列表复制到剪切板（等会我会单独介绍怎么准备这个文件列表）
3. 选择菜单上的“选择”-》“备份/回复选择列表”-》“从剪切板导入”
![从剪切板导入](/media/files/2015/06/12/load-selection-from-clip.png)
4. 拖动Total Command选中的文件列表到PNGoo中去
5. 完成


#### 准备文件列表
1. 以这个文件为例：  


> F:\test\Activity\100.png  

> F:\test\ActivityItem\ActivityItem0.png

2.Total Command，到F:\test\目录下，Ctrl+B把文件展开
3.将步骤1中的全路径，去掉步骤2中的目录前缀“F:\test\”，这时你将得到我们最终需要的文件列表，Ctrl+C复制到剪切板，文件选择列表搞定  



> Activity\100.png  

> ActivityItem\ActivityItem0.png


##### 小提示
1. SVN上的路径使用的是“/”，但是Windows下的文件路径是"\"，这个需要实现转换下  

2. 批量修改和删除字符串的操作，使用Sublime的话非常简单，我就不单独说了

这个方案勉强还可以接受，因为我习惯使用快捷键，上面的操作步骤我可以在30S内完成，而这样的事情我并不需要经常做，否则我想我应该写个脚本自动来做这个事情，看来还是不够懒。