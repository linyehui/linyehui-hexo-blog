---
title: '升级到Sierra后proxychains4 pod update失败'
layout: post
tags:
    - mac
    - pod
    - proxychains
---

## 问题
我的问题是proxychains4 telnet 是没问题的，但是proxychains4 pod update就会报下面这样的警告，然后失败：

> [proxychains] preloading ./libproxychains4.dylib
dyld: warning: could not load inserted library './libproxychains4.dylib' into library validated process because no suitable image found. Did find:
./libproxychains4.dylib: code signing blocked mmap() of './libproxychains4.dylib'

### 我的环境如下：
* macOS Sierra 10.12.3
* proxychains-ng 4.12_1
* SIP之前配置过用的是：csrutil enable --without debug，查询的状态如下：
```bash
$ csrutil status
System Integrity Protection status: enabled (Custom Configuration).

Configuration:
	Apple Internal: disabled
	Kext Signing: enabled
	Filesystem Protections: enabled
	Debugging Restrictions: disabled
	DTrace Restrictions: enabled
	NVRAM Protections: enabled
	BaseSystem Verification: enabled

This is an unsupported configuration, likely to break in the future and leave your machine in an unknown state.
```

## 寻找原因
一开始是找到了这篇issues:[Proxychains4 with brew for MacOs error #109](https://github.com/rofl0r/proxychains-ng/issues/109)，以为是我的proxychains4版本问题或者是我的SIP问题，所以做了以下尝试：
```
# 进入系统的recovery mode，禁用SIP
csrutil disable
reboot

# 重启后卸载老的proxychains
brew tap beeftornado/rmtree
brew rmtree proxychains-ng

# 重新安装
proxychains-ng --universal

# 确认下proxychains-ng的配置是否正确，如果不正确就再配置下proxychains.conf
# 确认下telnet是否正常
proxychains4 telnet google.com 80

# 再尝试下pod update
proxychains4 pod update

# 问题依旧：code signing blocked mmap()

```

## 问题所在
在另外一个issue78:[Not working on OS X 10.11 due to SIP #78](https://github.com/rofl0r/proxychains-ng/issues/78)中看到了被点赞了4次的这个回答，让我最后解决了问题：

> It only happens if you execute a system binary using proxychains, e.g. proxychains4 ssh user@server. For now, a workaround is to copy the executable to another location (e.g. cp /usr/bin/ssh ~/XXX), and use it (e.g. proxychains4 ~/XXX/ssh user@server). You can modify the path variable so that ~/XXX/ssh is executed instead of /usr/bin/ssh, when you just type "ssh".

### 一句话描述问题：
> proxychain尝试注入系统bin目录下的二进制文件会出现这种情况，解决方案就是换个非系统目录的文件来执行和注入就可以了。

### 解决方案
根据上面的问题描述，再考虑pod update的实际操作，其实就是调用git去更新，而我的git用的是系统自带的：
```
$ which git
/usr/bin/git

```

这样一来问题就变成了让pod update使用我自己安装的git就可以了：
```
brew install git

# 修改.bash_profile，增加下面的export配置，优先搜索/usr/local/bin目录
# 这样修改后，terminal下使用git就会优先使用我们刚刚安装的git版本了
export PATH=/usr/local/bin:/usr/local/sbin:${PATH}
```

好了，配置好了，再试一下，成功了:
```
proxychains4 pod update
```

### 关于SIP
最后我验证了下，SIP其实不需要disable，csrutil enable --without debug下proxychains也是能正常工作的

## 总结
说下我这里能够正常运行的环境和因素：
* macOS Sierra 10.12.3
* proxychains-ng 4.12_1
* 进入系统的recovery mode，打开terminal，csrutil enable --without debug
可以使用这个命令查询的状态，我的状态如下：
```bash
$ csrutil status
System Integrity Protection status: enabled (Custom Configuration).

Configuration:
	Apple Internal: disabled
	Kext Signing: enabled
	Filesystem Protections: enabled
	Debugging Restrictions: disabled
	DTrace Restrictions: enabled
	NVRAM Protections: enabled
	BaseSystem Verification: enabled

This is an unsupported configuration, likely to break in the future and leave your machine in an unknown state.
```
* 我的git使用的是brew install git的版本:
```
$ which git
/usr/local/bin/git
```
## 参考材料
[Not working on OS X 10.11 due to SIP #78](https://github.com/rofl0r/proxychains-ng/issues/78)
[Proxychains4 with brew for MacOs error #109](https://github.com/rofl0r/proxychains-ng/issues/109)
[code signing blocked mmap() #159](https://github.com/rofl0r/proxychains-ng/issues/159)