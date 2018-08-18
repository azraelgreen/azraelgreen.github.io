---
layout: post
title: ubuntu下vsftpd安装配置，及虚拟用户配置
date: 2018-08-18
categories: blog
tags: [vsftpd,ubuntu]
description: ubuntu下vsftpd安装配置，及虚拟用户配置

---

## 一、需求描述

1. 创建一个FTP账号 **ftpuser**，该账号只能登录到 **/www** 这个网站目录下，不能切换到上级目录。
2. 该账号上传的文件权限为644，即上传的文件具有可读可写权限，但是没有可执行权限。
3. 该帐号上传后的文件所有者为 **www-data**，即ubuntu系统下Nginx服务运行的默认用户。
4. 此外还要求该用户是vsftpd的**虚拟用户**。

操作系统以 **Ubuntu server 18.04 X64** 为例。

## 二、vsftpd安装

在配置vsftpd之前，我们先安装vsftpd：

`sudo apt-get -y install vsftpd`

在ubuntu下要启动、停止、重启vsftpd，相关命令：

	sudo service vsftpd stop
	sudo service vsftpd start
	sudo service vsftpd restart
	
Ubuntu下开机自动运行 vsftpd 服务：

	chkconfig vsftpd on

或者

	systemctl enable vsftpd

## 三、vsftpd配置

### 3.1 用户相关配置

因为是使用 vsftpd 的虚拟用户，所以需要在系统中有一个属主用户，并且该用户对 /www 目录具有可读可写可执行权限。

如果你**先安装过 Nginx**，再安装 vsftpd，那么系统已经自动建立了一个 **www-data** 用户。这步可以跳过。

创建 www-data 用户：

	sudo useradd -m -s /bin/bash www-data
	cat /etc/passwd |grep www-data

注意：创建的用户 www-data 现在是无法登录到系统的，因为没有给该用户设置密码。
在此，我们也无需让它登录到系统，这样相对来说比较安全。

用户创建完毕后，我们来创建对应的目录并修改其所属用户：

	sudo mkdir /www
	sudo chown -R www-data.www-data /www/

### 3.2 配置虚拟用户帐号密码

开始设置登录 vsftp 的用户与密码文件 **login.txt** ：

	sudo mkdir /etc/vsftpd/
	sudo vim /etc/vsftpd/login.txt

login.txt 内容如下（一行用户名，一行密码的格式）：

	ftpuser
	ftp_pwd

### 3.3 生成虚拟用户数据库文件

使用 db_load 进行加密。而 db_load 需要 db-util 这个软件。
所以需要我们现在安装db-util：

	sudo apt-get -y install db-util

db-util安装完毕后，用 db_load 对 loginx.txt 进行加密：

	sudo db_load -T -t hash -f /etc/vsftpd/login.txt /etc/vsftpd/login.db

### 3.4 PAM验证配置

> vsftpd 使用 PAM 验证，在此没有使用 vsftpd 安装时所生成的 /etc/pam.d/vsftpd 文件。

> 因为经过我多次的测试，发现如果使用该文件进行验证的话，无法验证通过。不知道为什么，猜想很有可能是vsftpd的一个BUG。

创建验证文件：

	sudo vim /etc/pam.d/vsftpd.virtual
	
vsftpd.virtual 内容如下：

	auth required pam_userdb.so db=/etc/vsftpd/login
	account required pam_userdb.so db=/etc/vsftpd/login

文件的内容，也可以根据OS的版本进行调整。使用的是ubuntu x64，也可以填写为（可选）：

	auth required /lib/x86_64-linux-gnu/security/pam_userdb.so db=/etc/vsftpd/login
	account required /lib/x86_64-linux-gnu/security/pam_userdb.so db=/etc/vsftpd/login

其中 /etc/vsftpd/login 对应 /etc/vsftpd/login.db 文件

### 3.5 vsftp权限配置

现在正式配置vsftpd，vsftpd 配置文件为 **/etc/vsftpd.conf **。

全部内容如下（可直接复制，并替换原全部内容）：

	listen=YES
	listen_ipv6=NO
	anonymous_enable=NO
	local_enable=YES
	write_enable=YES
	local_umask=022
	dirmessage_enable=YES
	use_localtime=YES
	xferlog_enable=YES
	connect_from_port_20=YES
	xferlog_file=/var/log/vsftpd.log
	xferlog_std_format=YES
	chroot_local_user=YES
	chroot_list_enable=NO
	allow_writeable_chroot=YES
	secure_chroot_dir=/var/run/vsftpd/empty
	pam_service_name=vsftpd
	rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
	rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
	ssl_enable=NO
	guest_enable=YES
	pam_service_name=vsftpd.virtual
	user_config_dir=/etc/vsftpd/vu
	pasv_enable=YES
	pasv_min_port=30000
	pasv_max_port=31000

#### 在以上配置文件中，有几点重点解释：

	local_enable=YES
	write_enable=YES
	local_umask=022

这几项是启用系统用户的写权限。特别是 **write_enable=YES **项一定要启用，否则 vsftpd 虚拟用户将无法登录 vsftpd。

原因：虚拟用户依赖于系统用户。

	chroot_local_user=YES
	chroot_list_enable=NO
	allow_writeable_chroot=YES

这三项是配置 vsftpd 用户禁止切换上级目录的权限。

	guest_enable=YES
	pam_service_name=vsftpd.virtual
	user_config_dir=/etc/vsftpd/vu

这三项是启用 vsftpd 虚拟用以及虚拟用户账号配置目录。

	pasv_enable=YES
	pasv_min_port=30000
	pasv_max_port=31000

这三项是启用 vsftpd 被动模式及相关端口。

### 3.6 虚拟用户相关配置

vsftpd 配置文件修改文件后，开始配置虚拟用户的相关权限：

	sudo mkdir /etc/vsftpd/vu
	sudo vim /etc/vsftpd/vu/ftpuser

ftpuser 文件内容如下：

	guest_username=www-data
	local_root=/www/
	virtual_use_local_privs=YES
	anon_umask=133


以上配置参数，其中 **guest_username=www-data** 表示的是设置 FTP 对应的系统用户为 www-data。

local_root=/www/  表示使用本地用户登录到ftp时的默认目录。
virtual_use_local_privs=YES  虚拟用户和本地用户有相同的权限。

anon_umask  表示文件上传的默认掩码。
计算方式是777减去 anon_umask 就是上传文件的权限。
在此我们设置的是133，也就是说上传后文件的权限是644。即上传的文件对所属用户来说只有读写权限，没有执行权限。

以上全部配置完毕后，来重启vsftpd：

`sudo service vsftpd restart`

## 四、测试

至此，vsftpd基本就配置完毕了。
我们可以在本地使用自己常用的FTP客户端（如 FileZilla ）来使用 ftpuser 用户登录 vsftpd 进行测试。

如果错误提示：

	响应:    500 OOPS: cannot change directory:/home/www
	错误:    严重错误: 无法连接到服务器

原因：可能是使用的属主目录（/home/www）没有建立

**解决方法：**

新建属主目录 

`sudo mkdir /home/www`

并把目录修改为属主权限

`chown www-data.www-data /home/www`

即可。


## 五、防火墙开放 vsftpd 对应端口（包括主动端口和被动端口）

IPtables 配置如下：

允许FTP服务的21和20端口

	iptables -A INPUT -p tcp --dport 21 -j ACCEPT
	iptables -A INPUT -p tcp --dport 20 -j ACCEPT

允许FTP服务的被动端口

	iptables -A INPUT -p tcp --dport 30000-31000 -j ACCEPT
	
如果使用的是阿里云服务器，则可以直接在阿里云网页后台，设定对应安全组，开放指定端口即可。
