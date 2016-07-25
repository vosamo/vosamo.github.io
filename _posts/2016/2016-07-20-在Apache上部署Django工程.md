---
layout: post
title: 在Apache上部署Django工程
categories:
- 计算机
tags:
- web
- Django
---

本文介绍一下在Centos 7.0中，在Apache上面部署Django工程的方法。假设你已经安装了Apache和Django，并且有一个完整的Django工程。本文基于的环境配置为：Centos 7.0+Apache 2.4.6+Django 1.8。

在Centos中Apache的配置文件为：`/etc/httpd/conf/httpd.conf`，打开文件添加如下内容：

```
Alias /static/ /path/to/mysite/static/
<VirtualHost *:8000>
    ServerName localhost:8000
    DocumentRoot /var/www/html
	WSGIDaemonProcess mysite processes=2 threads=250 display-name=mysite_daemon
	WSGIProcessGroup mysite
    # WSGIScriptAlias / /path/to/mysite/mysite/wsgi.py
    <Directory /path/to/mysite/mysite>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>
</VirtualHost>
```

很简单，不做太多说明。需要注意几点：

- `/path/to/mysite/`是Django工程的路径
- WSGIDaemonProcess和WSGIProcessGroup的名字要一致
- 如果设置了WSGIScriptAlias，就不用设置WSGIDaemonProcess和WSGIProcessGroup，这是两种配置方式
- 一定要设置静态文件路径