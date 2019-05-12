---
layout: post
title: 安装 Manjaro Linux 后，我会做的几件事情
date: 2019-05-12
categories: blog
tags: [linux]
description: 安装 Manjaro Linux 后，我会做的几件事情

---

Manjaro 是一个非常优秀的 Linux 发行版本。
它继承了 archlinux 的滚动升级的特征，但又不那么激进，保证了系统的稳定性。
像大部分发行版本一样，在国内使用首先要换源，Manjaro本身包含了很多国内的源。
使用下面命令可以自动测试各个源的速度。
本文将介绍如何Manjaro更换中文源与安装输入法。

`sudo pacman-mirrors -i -c China -m rank`

`sudo pacman -Syy`

对整个系统进行更新

`sudo pacman -Syu`



下面要编辑 pacman.conf，添加 archlinuxcn 源。
archlinuxcn 是一个由 Arch Linux 中文社区驱动的非官方用户仓库。

`sudo vi /etc/pacman.conf`

在文件最后添加

    [archlinuxcn]
    SigLevel = Optional TrustedOnly
    Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch

之后更新软件数据源

`sudo pacman -Syy`

`sudo pacman -S archlinux-keyring`

有了 archlinuxcn 源就可以安装输入法了。
安装时不能直接安装最后一个包，靠依赖安装 fcitx，这样会导致 fcitx 版本缺失。
须使用下面顺序安装，其中 fcitx-im 使用默认选项，安装每个版本的 fcitx。

    sudo pacman -S fcitx-im
    sudo pacman -S fcitx-configtool
    sudo pacman -S  fcitx-googlepinyin

安装好后编辑用户，使在每个环境下都使用 fcitx。编辑 ~/.xprofile 文件

`vi ~/.xprofile`

输入

    export GTK_IM_MODULE=fcitx
    export QT_IM_MODULE=fcitx
    export XMODIFIERS="@im=fcitx"

设置结束后重启即可在fcitx中找到输入法了。
