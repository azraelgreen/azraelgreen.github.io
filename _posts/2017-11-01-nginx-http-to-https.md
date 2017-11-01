---
layout: post
title: nginx配置 http 强制跳转 https 方法
date: 2017-11-01
categories: blog
tags: [nginx,ssl]
description: nginx配置 http 强制跳转 https 方法

---

### 一、最常用的方法：rewrite 方法

将所有 http 请求通过 rewrite 重定向到 https 即可

nginx.conf 配置文件：

	server {  
	    listen  80;
	    server_name www.test.com;

		rewrite ^(.*)$  https://$host$1 permanent;  
	}

	server {
    	listen 443 ssl;
    	server_name www.test.com;
    	index index.html index.htm;
    	access_log  /var/log/nginx/docs.log  main;
    	ssl on;
    	ssl_certificate /etc/ssl/docs.20150509.cn.crt;
    	ssl_certificate_key  /etc/ssl/docs.20150509.cn.key;
    	error_page 404 /404.html;
    	location / {
     		root /var/www/html/docs;
    	}
	}



### 二、利用 index.html 的 meta 刷新，来实现 http 向 https 的跳转：

index.html 文件内容：

	<html>  
    	<meta http-equiv="refresh" content="0;url=https://www.test.com/">
	</html>

nginx.conf 配置文件：

	server {
    	listen 80;
    	server_name www.test.com;

	location / {
        # 将 index.html 文件放到下面的目录下
        root /var/www/html/refresh/;
    	}
	}

	server {
    	listen 443 ssl;
    	server_name www.test.com;
    	index index.html index.htm;
    	access_log  /var/log/nginx/docs.log  main;
    	ssl on;
    	ssl_certificate /etc/ssl/docs.20150509.cn.crt;
    	ssl_certificate_key  /etc/ssl/docs.20150509.cn.key;
    	error_page 404 /404.html;

    	location / {
     		root /var/www/html/docs;
    	}
	}

