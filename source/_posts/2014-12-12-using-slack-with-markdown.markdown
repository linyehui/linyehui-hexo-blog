---
title: 'Using Slack with Markdown'
layout: post
tags:
    - slack
    - markdown
---

> Slack is awesome, but multiline code formatting in a Slack Post is sucks.
My solution is using [Markdown Preview Plus](https://chrome.google.com/webstore/detail/markdown-preview-plus/febilkbfcbhebfnokafefeacimjdckgl?utm_source=chrome-ntp-icon) with a Slack Snippet.

# 需求
使用Slack做运维日志的记录，不可避免的会有一些代码或者脚本，也想直接记录在Slack中。  

解决这个问题，直接想到的就是"create a post"，Slack的POST虽然是支持Markdown的，但是他的支持非常有限，最让我无法忍受的是多行代码的排版，会完全乱掉。  

能不能再Slack中使用Markdown而且有更好的排版支持呢？这是我的需求。

PS.  

补充一个使用情况：在PC或Mac上，我都是优先选择使用Web版本，Slack我也是在我的Chrome中使用。

# 解决方案

## 0.没有用的吐槽
发了条[Twitter](https://twitter.com/linyehui/status/542993353340493824):
> @SlackHQ Slack's Code Formatting with Markdown is sucks :( #markdown

发现Twitter也有人反馈过这个问题，Slack的回复很官方，但是实际的效果就是排版惨不忍睹。

## 1.Markdown Preview Plus(Chrome Extension) + text snippet

## 解决方案描述
1. 使用Chrome进行以下操作
2. 安装[Markdown Preview Plus](https://chrome.google.com/webstore/detail/markdown-preview-plus/febilkbfcbhebfnokafefeacimjdckgl?utm_source=chrome-ntp-icon)
3. 在Slack中"create a text Snippet"
4. 使用Markdown进行编辑文档
5. 使用"View raw"来查看Markdown文件，而插件会自动帮你格式化排版

## 折中方案
这其实是一个折中的解决方案，因为他缺失了一点功能：post分享出去的时候是带格式的，而text snippet分享出去是源代码格式，但是基于目前Slack Post的排版如此惨不忍睹，其实这个舍弃是完全可以接受的。

text snippet选择Markdown类型，没有格式预览，但源代码显示是正常的，也有语法高亮，需要预览最样式就使用Markdown Preview Plus，够用了。

