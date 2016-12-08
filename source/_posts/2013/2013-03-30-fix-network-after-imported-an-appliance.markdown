---
title: 'VirtualBox导入虚拟电脑后找不到网卡'
layout: post
tags:
    - virtualbox
---

**问题：**  

使用了VirtualBox的导入和导出功能，用来实现类似VM的“克隆”功能，但是发现导出后，再次导入的虚拟机，如果我选择了
> "Reinitialize the MAC address of all network cards"（重新初始化所有网卡的MAC地址），

那么在导入完成后你会发现系统无法找到网卡eth0了

**解决方法：**  


```shell
$cd /etc/udev/rules.d
$sudo mkdir backup
$sudo mv * backup
#sudo reboot
```

**参考：**  

[【转】跟我一起写udev规则(译)](http://www.cnitblog.com/luofuchong/archive/2007/12/18/37831.html)  

[VirtualBox虚拟机Ubuntu找不到网卡的解决方法](http://www.blogjava.net/zhyiwww/archive/2010/12/31/312612.html)  
