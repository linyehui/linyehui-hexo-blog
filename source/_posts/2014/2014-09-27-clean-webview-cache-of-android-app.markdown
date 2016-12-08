---
title: 'Android下手动清除WebView缓存'
layout: post
tags:
    - cache
    - webview
    - android
---

# 需求
手动清除Android QQ中内嵌WebView缓存

# 寻找解决方案
[WebView Cache 缓存清除](http://blog.csdn.net/moubenmao_jun/article/details/17078269)

### 简单说，就是应用内的WebView缓存都会序列化在这里：
```
/data/data/package_name/cache/
/data/data/package_name/database/webview.db
/data/data/package_name/database/webviewCache.db
```

# 手动删除缓存-操作步骤
* 杀掉应用
* 去/data/data/package_name/目录下删除cache目录
* 去/data/data/package_name/database/目录下删除所有webView前缀的文件
* 搞定