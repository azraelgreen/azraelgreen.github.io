---
layout: post
title: 如何汉化darktable？
date: 2021-02-28
categories: blog
tags: [Win]
description: 如何汉化darktable？

---

## 第一步：下载 darktable

官网： [https://www.darktable.org](https://www.darktable.org)



## 第二步：下载 "zh_CN.po" 文件

下载地址：[https://raw.githubusercontent.com/darktable-org/darktable/master/po/zh_CN.po](https://raw.githubusercontent.com/darktable-org/darktable/master/po/zh_CN.po)



## 第三步：安装poedit，编译 po 文件为 mo 文件

#### Linux：

`sudo apt-get install poedit`

`msgfmt zh_CN.po -o darktable.mo`

#### Win：

下载 poedit：[https://poedit.en.softonic.com](https://poedit.en.softonic.com)

另存为 mo 文件，文件名保存为 darktable.mo


也可以直接使用我编译好的 darktable.mo 文件: [https://azraelgreen.github.io/darktable.mo](https://azraelgreen.github.io/darktable.mo)



## 第四步：将darktable.mo拷贝到darktable安装目录

目录位置：`X:\Program Files\darktable\share\locale\zh_CN\LC_MESSAGES`



## 第五步： 打开darktable，设置语言为"汉语"，重启软件



PS：不时有网友留言说打不开第1条的链接，今天试了下，通过一定方法还是可以打开的。

不过打不开也没关系，我重新下了最新的zh_CN.po汉化源文件，也编译了一份最新的 darktable.mo，放在我的百度网盘里。里面的txt文本文件包含了汉化方法，文件名标注了什么时候下载的文件（我会不定期更新 zh_CN.po 汉化文件，和最终的 darktable.mo 文件）。

百度网盘

链接: https://pan.baidu.com/s/1QxoBw7KOIh8kGG3HPw8V0Q

提取码: 47ww