---
layout: post
title: 配置nginx让所有二级域名都跳转到www
date: 2017-11-17
categories: blog
tags: [nginx]
description: 配置nginx让所有二级域名都跳转到www

---

域名泛解析是为了担心用户输入错误而打不开网站而设置的

比如用户 `www.abc.com` 输入成 `ww.abc.com`，

### 解决方法一：

我们在**域名解析**，添加一条 `*` 指向服务器 `ip`。就可以避免用户因为输入错的二级域名，而打不开网站的情况。

但是我们还要考虑到泛解析对网站seo的影响

### 更好的解决方法二：

为了避免这种情况对 seo 的影响我们就在 nginx 配置文件里面加入下面的配置，就可以实现所以二级域名都跳转到 www 上面。让搜索引擎只收录我们的 www 网页。

	server {
	     listen       80;
	     server_name *.abc.com;
	     rewrite ^(.*) http://www.abc.com$1 permanent;
	}

