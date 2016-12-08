---
title: 'Rime 鼠须管输入法'
layout: post
tags:
    - rime
    - mac
---

## 参考文档
* [Rime 定製指南](https://github.com/rime/home/wiki/CustomizationGuide)  

* [RimeWithSchemata](https://github.com/rime/home/wiki/RimeWithSchemata)  

* [「鼠須管」的调教笔记](https://medium.com/@scomper/%E9%BC%A0%E9%A0%88%E7%AE%A1-%E7%9A%84%E8%B0%83%E6%95%99%E7%AC%94%E8%AE%B0-3fdeb0e78814#.oq28ijk3h)  

* [Rime输入法—鼠须管(Squirrel)词库添加及配置](http://www.jianshu.com/p/cffc0ea094a7)  


## 配置入门

### 说在前面的几点提示
* 1、不推荐使用图形化配置工具，直接修改配置文件是最靠谱的  

* 2、配置文件在~/Library/Rime这个目录下  

* 3、不要直接修改配置文件，使用.custom.yaml的扩展配置来自定义自己的配置  


### 配置文件说明
注意下面的文件，都是对默认文件的自定义，所有文件名中都有".custom"  



```
squirrel.custom.yaml ，自定义皮肤；  

default.custom.yaml ，设定备选词数量，定义输入方案；  

luna_pinyin_simp.custom.yaml ，定义扩充词库、加载符号库、模糊拼音。明月拼音·简化字输入方案配置文件，明月拼音对应的文件是 luna_pinyin.custom.yaml；  

```

### 还原默认配置文件
万一你不听劝，还是修改来默认的配置文件，而且改坏来，那么有个简单的方法可以还原：  



```
把~/Library/Rime目录删除，然后点击下“重新部署”，会自动生成默认配置。
```

### 切换到简体输入
```
Ctrl+~
选择“朙月拼音.简化字”
```

### 切换全角和半角


```
shift+空格

也可以使用Ctrl+~进行切换，不过就要多进行一次选择操作
```


### squirrel.custom.yaml
指定程序的名称（比如 Safari 浏览器的程序名称 com.apple.Safari ），通过在「应用程序」里选中 app 图标，鼠标右键选择「显示包内容」，然后找到包内文件夹里的「Info.plist」文件，找到其中 Bundle identifier 属性，对应的就是指定程序名称了。

```
patch:
  style:
    horizontal: true
    font_point: 32
    color_scheme: starcraft
    horizontal : true # 候选词横排
    font_face" : "苹方-简 细体" # 字体名称
  app_options:
    com.alfredapp.Alfred:
      ascii_mode: true
    com.runningwithcrayons.Alfred-2:
      ascii_mode: true
    com.blacktree.Quicksilver:
      ascii_mode: true
    com.apple.Terminal:
      ascii_mode: true
      no_inline: true
    com.googlecode.iterm2:
      ascii_mode: true
    org.vim.MacVim:
      ascii_mode: true
      no_inline: true
    com.apple.dt.Xcode:
      ascii_mode: true
    com.apple.spotlight:
      ascii_mode: true
    com.apple.finder:
      ascii_mode: true
    com.sublimetext.3:
      ascii_mode: true
```

### luna_pinyin.custom.yaml

```python
# luna_pinyin.custom.yaml
#
# 【朙月拼音】模糊音定製模板
#   佛振配製 :-)
#
# 位置：
# ~/.config/ibus/rime  (Linux)
# ~/Library/Rime  (Mac OS)
# %APPDATA%\Rime  (Windows)
#
# 於重新部署後生效
#

patch:
  'speller/algebra':
    - erase/^xx$/                      # 第一行保留

    # 模糊音定義
    # 需要哪組就刪去行首的 # 號，單雙向任選
    - derive/^([zcs])h/$1/             # zh, ch, sh => z, c, s
    - derive/^([zcs])([^h])/$1h$2/     # z, c, s => zh, ch, sh

    #- derive/^n/l/                     # n => l
    #- derive/^l/n/                     # l => n

    # 這兩組一般是單向的
    - derive/^r/l/                     # r => l

    #- derive/^ren/yin/                 # ren => yin, reng => ying
    #- derive/^r/y/                     # r => y

    # 下面 hu <=> f 這組寫法複雜一些，分情況討論
    #- derive/^hu$/fu/                  # hu => fu
    #- derive/^hong$/feng/              # hong => feng
    #- derive/^hu([in])$/fe$1/          # hui => fei, hun => fen
    #- derive/^hu([ao])/f$1/            # hua => fa, ...

    #- derive/^fu$/hu/                  # fu => hu
    #- derive/^feng$/hong/              # feng => hong
    #- derive/^fe([in])$/hu$1/          # fei => hui, fen => hun
    #- derive/^f([ao])/hu$1/            # fa => hua, ...

    # 韻母部份
    #- derive/^([bpmf])eng$/$1ong/      # meng = mong, ...
    - derive/([eia])n$/$1ng/            # en => eng, in => ing, an => ang
    - derive/([eia])ng$/$1n/            # eng => en, ing => in, ang => an

    # 樣例足夠了，其他請自己總結……

    # 反模糊音？
    # 誰說方言沒有普通話精確、有模糊音，就能有反模糊音。
    # 示例爲分尖團的中原官話：
    #- derive/^ji$/zii/   # 在設計者安排下鳩佔鵲巢，尖音i只好雙寫了
    #- derive/^qi$/cii/
    #- derive/^xi$/sii/
    #- derive/^ji/zi/
    #- derive/^qi/ci/
    #- derive/^xi/si/
    #- derive/^ju/zv/
    #- derive/^qu/cv/
    #- derive/^xu/sv/
    # 韻母部份，只能從大面上覆蓋
    #- derive/^([bpm])o$/$1eh/          # bo => beh, ...
    #- derive/(^|[dtnlgkhzcs]h?)e$/$1eh/  # ge => geh, se => sheh, ...
    #- derive/^([gkh])uo$/$1ue/         # guo => gue, ...
    #- derive/^([gkh])e$/$1uo/          # he => huo, ...
    #- derive/([uv])e$/$1o/             # jue => juo, lve => lvo, ...
    #- derive/^fei$/fi/                 # fei => fi
    #- derive/^wei$/vi/                 # wei => vi
    #- derive/^([nl])ei$/$1ui/          # nei => nui, lei => lui
    #- derive/^([nlzcs])un$/$1vn/       # lun => lvn, zun => zvn, ... 
    #- derive/^([nlzcs])ong$/$1iong/    # long => liong, song => siong, ...
    # 這個辦法雖從拼寫上做出了區分，然而受詞典制約，候選字仍是混的。
    # 只有真正的方音輸入方案纔能做到！但「反模糊音」這個玩法快速而有效！

    # 模糊音定義先於簡拼定義，方可令簡拼支持以上模糊音
    - abbrev/^([a-z]).+$/$1/           # 簡拼（首字母）
    - abbrev/^([zcs]h).+$/$1/          # 簡拼（zh, ch, sh）

    # 以下是一組容錯拼寫，《漢語拼音》方案以前者爲正
    - derive/^([nl])ve$/$1ue/          # nve = nue, lve = lue
    - derive/^([jqxy])u/$1v/           # ju = jv,
    - derive/un$/uen/                  # gun = guen,
    - derive/ui$/uei/                  # gui = guei,
    - derive/iu$/iou/                  # jiu = jiou,

    # 自動糾正一些常見的按鍵錯誤
    - derive/([aeiou])ng$/$1gn/        # dagn => dang 
    - derive/([dtngkhrzcs])o(u|ng)$/$1o/  # zho => zhong|zhou
    - derive/ong$/on/                  # zhonguo => zhong guo
    - derive/ao$/oa/                   # hoa => hao
    - derive/([iu])a(o|ng?)$/a$1$2/    # tain => tian

  # 分尖團後 v => ü 的改寫條件也要相應地擴充：
  #'translator/preedit_format':
  #  - "xform/([nljqxyzcs])v/$1ü/"

```

### 全部使用英文符号
默认输入中括号，出来的是花式的括号，有点不太习惯，从官方下载这个文档[alternative.yaml](https://gist.github.com/lotem/2334409)放到配置目录，然后修改下luna_pinyin.custom.yaml

```
# luna_pinyin.custom.yaml

patch:
  'punctuator/import_preset': alternative
```

这样就能直接用"[]"输出"[]"了
