---
title: '微信JS-SDK联调'
layout: post
tags:
    - weixin
    - tips
    - javascript
---

## 需求
在微信中打开我们的网页，网页能够使用微信提供的JS-SDK，比如使用chooseImage，选择图片等等

## 坑

### AppID
要使用公众账号的开发AppID，而不是微信开发平台下的应用AppID

### 业务域名和JS接口安全域名
1、不设置会导致签名失败  

2、设置方法  

在微信公众平台的后台，公众设置，功能设置中进行设置，业务域名和JS接口安全域名都是需要设置的  


### 签名失败
遇到签名失败，首先根据微信官方的指引：[《附录5-常见错误及解决方法》](http://mp.weixin.qq.com/wiki/7/1c97470084b73f8e224fe6d9bab1625b.html#.E9.99.84.E5.BD.955-.E5.B8.B8.E8.A7.81.E9.94.99.E8.AF.AF.E5.8F.8A.E8.A7.A3.E5.86.B3.E6.96.B9.E6.B3.95)，看看能不能解决。

#### 我自己踩坑之后的理解如下：    

1、签名用的url要动态获取  

> location.href.split('#')[0]

2、appID不要用错了，要用公众平台的开发appID  


3、怀疑签名算法有问题，可以用微信提供的签名校验网址来确认：[微信 JS 接口签名校验工具](http://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=jsapisign)  


4、文档中说了“出于安全考虑，开发者必须在服务器端实现签名的逻辑。”，这个不是强制的，在前端静态页面直接获取到token后再去获取ticket，然后直接生成签名，也是可以的。    

当然这样做并不安全，只是说可以这样子，微信并没有做代码级的限制。  


5、JS-SDK的调通跟公众平台后台的"开发-》基本配置-》服务器配置(未启用)"没有关系。    


## 参考的材料
[微信JS-SDK说明文档](http://mp.weixin.qq.com/wiki/7/1c97470084b73f8e224fe6d9bab1625b.html)  

[如何接入微信公众平台（nodejs）](http://pigerla.com/wechat-api/2015-06-23/using-js-sdk-of-WeChat/)  

[微信JS-SDK分享功能开发实例](http://m.oschina.net/blog/384705)  

[微信JSSDK Python版本讲解](http://www.smallerpig.com/889.html)  

[微信JS-SDK 接入步骤](https://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html#JSSDK.E4.BD.BF.E7.94.A8.E6.AD.A5.E9.AA.A4)  

[基于node.js的微信JS-SDK简单应用](http://www.cocoachina.com/webapp/20150708/12465.html)  

[微信官方提供的sample](http://203.195.235.76/jssdk/sample.zip)  

[微信 JS 接口签名校验工具](http://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=jsapisign)  

