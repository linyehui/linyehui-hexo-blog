---
title: 'iOS Provisioning Profiles'
layout: post
tags:
    - mac
    - ios
    - debug
---

## 问题
如果你遇到过XCode下的开发者证书怎么都无法正确获取，无法真机调试，各种抓狂，那么你应该也非常需要下面的吐槽和解决方案。

## 解决方案:一切重头再来
重头再来不是说，安装教程从头到尾再操作一次，因为你会抓狂，基本上你应该已经按照教程操作的多次，所以这里说的重头再来，我想告诉大家的是：

### 如何清除本地的证书信息
1. 打开"钥匙串"，切换到证书，删除所有Apple Developer相关的证书
2. 打开XCode，Performance,Account,删除之前的登录信息，重新登录开发者账号
3. 删除本地的证书，服务器上配置的iOS Provisioning Profiles (Development)其实也是可以删除的，不删除也没问题，因为使用XCode重新申请证书的话，会自动把老的配置置为无效
![证书](/media/files/2015/03/27/12_revoke_certificate_2_2x.png)

做了上面的事情，你就可以安装各种教程，重新申请下证书了，这时你应该已经可以成功了。  


参考：[Maintaining Your Signing Identities and Certificates](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html#//apple_ref/doc/uid/TP40012582-CH31-SW26)
[Valid signing identity not found（有关真机调试）](http://www.cocoachina.com/bbs/read.php?tid=256724)  


这里有一点需要特别说的是：
### No signing identity found 不影响真机调试
如果你已经重新来过了，而且Build Setting-》Code Signing-》Provisioning Profiles已经有了正确的配置文件了，那么这个时候你看到  


```
No signing identity found 
```

这个提示是可以忽略的，已经可以真机调试了。

## Certificates, Identifiers & Profiles
Provisioning Profiles 到底是干什么的？  

简单说，这个配置文件做的事情就是校验调试流程的三个主体的合法性：苹果开发者，XCode使用者，iPhone使用者。

所以在developer.apple.com后台生成Provisioning Profiles的时候，你需要分别选择：

1. 开发者的证书（手动生成的时候是需要本地调用钥匙串来生成秘钥的）  

2. App ID，应用的名称，可以使用通配符  

3. Deviece ID，设备ID  


## 我为什么会遇到这个问题
公司的开发者账号很容易满员，所以我加入了三个开发者账号……
