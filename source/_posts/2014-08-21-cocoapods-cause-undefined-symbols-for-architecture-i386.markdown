---
title: '使用CocoaPods引入第三方库导致的Undefined symbols for architecture i386'
layout: post
tags:
    - cocoapods
    - cocos2dx
    - fmdb
    - ios
---

# 问题
---
* 使用cocos2dx的iOS工程，通过cocospods引入了FMDB用于使用SQLite：
> pod 'FMDB', '~> 2.3'

* Xcode 5.1.1

* 结果选择64的模拟器编译时提示

```
Undefined symbols for architecture i386:
  "_OBJC_CLASS_$_FMDatabase", referenced from:
      objc-class-ref in DBWrapper.o
ld: symbol(s) not found for architecture i386
```
  

# 问题分析
---
## 尝试
* 一开始搜索到的结果，出现这种情况都是因为没有把FMDB下的.m文件加入到工程里面；
* 但是我们其实是通过CocoaPods进行第三方库依赖的，这种情况还要自己把.m加到工程就太可笑了
* 还有一个疑问是为什么非64的模拟器就可以编译通过？
* 为了验证上面这个问题，我在github上找了个同样使用pod ‘FMDB’的例子，发现例子可以再64位下正常编译，也就是说和FMDB库是无关的，是我们自己使用的不对
* 盯着64位这个字眼，突然想起了我们的工程因为为了支持cocos2dx，是修改了编译选项中的      


```
config.build_settings['ARCHS'] = 'armv7 armv7s'
config.build_settings['ONLY_ACTIVE_ARCH'] = 'NO'
```

* 在Xcode下点击Pods工程，把新加入的FMDB-Pods target选项修改了下，果然就编译通过了

## 问题原因
* 使用到的两个第三方库的ONLY_ACTIVE_ARCH选项不同导致了这个问题
* cocos2dx为了兼容64位需要把这个选项设置位NO，而默认这个选项为YES

# 解决方案
---
### 解决的思路
* cocos2dx（2.2.4）因为本身不支持64位，所以ONLY_ACTIVE_ARCH需要设置为NO，那么有没有方法把github上第三方的Spec也使用我们的这个属性配置？

* 问题清楚后，搜索新的关键字马上就得到了答案：  

用CocosPods 的Podfile配置可以满足我的需求:[post install](http://guides.cocoapods.org/syntax/podfile.html#post_install)

* 然后我又去github上用post install位关键字找到好多类似的例子  

[Podfile 参考1](https://github.com/needbee/nbasyncrestconnection/blob/ecadaf7206ffddfd9cc829b880ba015d33be9eb0/demo/Podfile)  

[Podfile 参考2](https://github.com/cybertk/libchromium/blob/fa7fecdce6dbe31d8da82b80c999268bbea2ddf3/Example/Podfile)  

[Podfile 参考3](https://github.com/mikefullerton/FishLampLib/blob/3d9f0308015edab41335641dc0cdd7f3e47d63ea/Podfile)  


### 最终的方案
 有了上面的信息，我的解决方案也就水到渠成了：

```
pod 'FMDB', '~> 2.3'

post_install do |installer_representation|
  installer_representation.project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['ARCHS'] = 'armv7 armv7s'
      config.build_settings['ONLY_ACTIVE_ARCH'] = 'NO'
    end
  end
end
```

