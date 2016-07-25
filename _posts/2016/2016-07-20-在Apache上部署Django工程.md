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

## Apache配置

在Centos中Apache的配置文件为：`/etc/httpd/conf/httpd.conf`，打开文件添加如下内容：

```
Alias /static/ /path/to/mysite/static/
<VirtualHost *:8000>
    ServerName localhost:8000
    DocumentRoot /var/www/html
	WSGIDaemonProcess mysite processes=2 threads=250 display-name=mysite_daemon
	WSGIProcessGroup mysite
    WSGIScriptAlias / /path/to/mysite/mysite/wsgi.py
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
- 一定要设置静态文件路径

## 修改wsgi.py文件

上面如果写了 WSGIDaemonProcess 的话，这一步可以跳过，即可以不修改 wsgi.py 文件。

上面的配置文件中写的`WSGIScriptAlias / /path/to/mysite/mysite/wsgi.py`就把Apache和你的Django工程联系起来。

{% highlight python %}
import os
import sys
from django.core.wsgi import get_wsgi_application
from multiprocessing import Process
from django.conf import settings

BASE_DIR = os.path.dirname(os.path.dirname(__file__))
sys.path.insert(0, BASE_DIR)

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")

application = get_wsgi_application()
{% endhighlight %}

其中`sys.path.insert(0, BASE_DIR)`的作用是让脚本找到Django工程的位置，也可以在httpd.conf文件中用WSGIPythonPath来配置。

重启Apache之后，就可以通过Apache访问Django工程了。

部署时文件的相互联系：`httpd.conf --> wsgi.py --> settings.py --> urls.py --> views.py`