---
layout: post
title: 禁止特定ftp用户访问文件夹下指定文件或文件夹
date: 2018-01-09
categories: blog
tags: [vsftpd]
description: 禁止特定ftp用户访问文件夹下指定文件或文件夹

---

### 最近遇到一个很少见的需求：

在Linux系统下，使用vsftpd搭建的FTP环境。需要禁止一个FTP用户，访问其主目录下的特定文件夹！因为这个主目录是多个用户共用的，又要允许其他用户可以访问。要如何实现？

在网上一番搜索无果后，只好访问官方的配置文档看看。

官方 **vsftpd.conf** 配置文件参数
网址：https://security.appspot.com/vsftpd/vsftpd_conf.html

### 找到如下如下参数：

**deny_file**

>> This option can be used to set a pattern for filenames (and directory names etc.) which should not be accessible in any way. The affected items are not hidden, but any attempt to do anything to them (download, change into directory, affect something within directory etc.) will be denied. This option is very simple, and should not be used for serious access control - the filesystem's permissions should be used in preference. However, this option may be useful in certain virtual user setups. In particular aware that if a filename is accessible by a variety of names (perhaps due to symbolic links or hard links), then care must be taken to deny access to all the names. Access will be denied to items if their name contains the string given by hide_file, or if they match the regular expression specified by hide_file. Note that vsftpd's regular expression matching code is a simple implementation which is a subset of full regular expression functionality. Because of this, you will need to carefully and exhaustively test any application of this option. And you are recommended to use filesystem permissions for any important security policies due to their greater reliability. Supported regex syntax is any number of *, ? and unnested {,} operators. Regex matching is only supported on the last component of a path, e.g. a/b/? is supported but a/?/c is not. Example: deny_file={*.mp3,*.mov,.private}

>> Default: (none)

按照官方说明，这个权限控制方法不是优先推荐的（优先推荐使用linux系统的权限控制），但很适合于某个FTP虚拟用户（正好我搭建 vsftpd 也是使用的虚拟用户设置），用于其文件夹中的访问控制。

### 用法：

在这个FTP虚拟用户的配置文件中，添加：

`deny_file=文件夹名字`

deny_file 后面也可以使用更复杂的正则表达式，如

`deny_file={*.mp3,*.mov,.private}`
