---
layout: post
title: 如何汉化darktable？(Win系统)
date: 2019-08-18
categories: blog
tags: [Win]
description: 如何汉化darktable？(Win系统)

---

## 第一步：下载 darktable

官网： https://www.darktable.org



## 第二步：下载 "zh_CN.po" 文件

下载地址：https://raw.githubusercontent.com/darktable-org/darktable/master/po/zh_CN.po



## 第三步：安装poedit，编译 po 文件为 mo 文件

#### Linux：

`sudo apt-get install poedit`

`msgfmt zh_CN.po -o darktable.mo`

#### Win：

下载 poedit：https://poedit.en.softonic.com

另存为 mo 文件，文件名保存为 darktable.mo


也可以直接使用我编译好的 darktable.mo 文件: https://azraelgreen.github.io/darktable.mo



## 第四步：将darktable.mo拷贝到darktable安装目录

目录位置：`X:\Program Files\darktable\share\locale\zh_CN\LC_MESSAGES`



## 第五步： 打开darktable，设置语言为"汉语"，重启软件
