---
title: 'VirtualBox下的Ubuntu 12.04 配置桥接网络上网'
layout: post
tags:
    - virtualbox
---

**最终有用的方法是：**  

1.打开virtualbox，选择【设置】-【网络】将连接方式改为【Bridged Adapter】  

2.在Ubuntu中，
运行sudo vi /etc/network/interfaces  

修改如下:  


```
auto lo
iface lo inet loopback
auto eth0
iface eth0 inet static
address 172.17.1.178
netmask 255.255.0.0  #此处改成您的掩码
gateway 172.17.1.111 #此处改成您的网关
dns-search example.com
dns-nameservers 192.168.3.45 192.168.8.10
```

3.还是不行的话就执行下:  

resolvconf --disable-updates  

这个步骤是我后面google出来的综合结论，Ubuntu 12.04 在DNS这块有所改动导致的这一问题

**参考：**  

[How do I add changes to resolv.conf without them getting overwritten on reboot?](http://askubuntu.com/questions/157154/how-do-i-add-changes-to-resolv-conf-without-them-getting-overwritten-on-reboot)  

[How do I set the dns search domain in Ubuntu 12.04?](http://serverfault.com/questions/414879/how-do-i-set-the-dns-search-domain-in-ubuntu-12-04)  
