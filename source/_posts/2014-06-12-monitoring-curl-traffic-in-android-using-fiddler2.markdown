---
title: 'Android下使用Fiddler2对cURL进行抓包分析'
layout: post
tags:
    - android
    - curl
    - monitoring
---

### Android下对应用进行抓包分析，有很多文章已经介绍过了，大家可以参考：

**我的实际操作参考的是这篇：**
[Android利用Fiddler进行网络数据抓包](http://www.trinea.cn/android/android-network-sniffer/)

**其他的参考文章：**
[android应用安全——（数据抓包）跟踪监控android数据包](http://blog.csdn.net/xyz_lmn/article/details/8808169)

[Android抓包方法(一)之Fiddler代理](http://www.cnblogs.com/findyou/p/3491014.html)
[Android抓包方法(二)之Tcpdump命令+Wireshark](http://www.cnblogs.com/findyou/p/3491035.html)
[Android抓包方法(三)之Win7笔记本Wifi热点+WireShark工具](http://www.cnblogs.com/findyou/p/3491065.html)

### 我们今天的问题是：

Android怎么对cURL进行抓包？
或者说为什么普通的抓包方法不能抓到cURL的网络数据包？

### 原因

cURL不会直接使用系统的代理设置，所以使用HTTP代理来抓包的方式，默认情况下都是无法抓取到cURL的数据包的

### 解决方案

cURL设置下代码就好了（我嚓咧，这么简单……）

以我的Fiddler2为例，代码中设置下这个代理就能顺利抓取到cURL的数据包了

    curl_easy_setopt(m_pCurlHandle, CURLOPT_PROXY, "192.168.123.1:8888");
    curl_easy_setopt(m_pCurlHandle, CURLOPT_PROXYTYPE, CURLPROXY_HTTP);


### cURL超时怎么处理

**处理的原则是：**
1. CURLOPT_TIMEOUT和CURLOPT_CONNECTTIMEOUT都必须设置！
2. 需要的话可以把CURLOPT_DNS_CACHE_TIMEOUT 设置长一点；
3. 多线程使用cURL，需要设置CURLOPT_NOSIGNAL 

我们的代码一开始没有设置CURLOPT_CONNECTTIMEOUT，结果偶尔会遇到服务器返回502超时，看log最长的一次竟然卡了93秒……

#### cURL参数说明


    CURLOPT_TIMEOUT 设置cURL允许执行的最长秒数。  

    CURLOPT_TIMEOUT_MS 设置cURL允许执行的最长毫秒数。 
    

    CURLOPT_CONNECTTIMEOUT 在发起连接前等待的时间，如果设置为0，则无限等待。  

    CURLOPT_CONNECTTIMEOUT_MS 尝试连接等待的时间，以毫秒为单位。如果设置为0，则无限等待。 
    

    CURLOPT_DNS_CACHE_TIMEOUT 设置在内存中保存DNS信息的时间，默认为120秒。


#### C++代码


    curl_easy_setopt(_handler,CURLOPT_TIMEOUT,_timeout);  

            curl_easy_setopt(_handler,CURLOPT_CONNECTTIMEOUT_MS,_connect_timeout_ms);  

            curl_easy_setopt(_handler,CURLOPT_DNS_CACHE_TIMEOUT,_dns_cache_timeout);  

            curl_easy_setopt(_handler,CURLOPT_NOSIGNAL,1);



### 关于cURL的超时处理，这几篇文章值得参考:

[curl超时问题追查](http://hi.baidu.com/taibaishu/item/3962d901241b3c27a1312df8)
[初窥curl(上)](http://hi.baidu.com/farmerluo/item/7e1e56ffa394efee1b111f81)
[Libcurl多线程crash问题](http://blog.csdn.net/delphiwcdj/article/details/18284429)
[小谈libcurl](http://blog.csdn.net/linuxphilosopy/article/details/8864590)


-----End-----