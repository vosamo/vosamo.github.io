---
layout: post
title: RabbitMQ的使用（Agent和Server发送消息）
categories:
- 分布式
tags:
- 消息队列
---

使用RabbitMQ可以进行不同机器之间的消息传递。这篇文章就简要介绍一下如何通过RabbitMQ实现agent节点和server节点之间的消息传递。

简便起见，我们假设只有一台agent节点和一台server节点，在server节点上安装了RabbitMQ-server，agent节点发送一个消息到消息队列，server节点从队列中读取消息。

agent端的代码send.py：

{% highlight python %}
#!/usr/bin/env python
# coding=utf-8
import pika

# credentials = pika.PlainCredentials('admin','admin')
# host, port, vhost  '192.168.137.189'为server节点的IP
conn_params = pika.ConnectionParameters('192.168.137.189', 5672, '/')
connection = pika.BlockingConnection(conn_params)
channel = connection.channel()
channel.queue_declare(queue='hello')

channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
print "[x] sent 'Hello World!'"

connection.close()
{% endhighlight %}

server端的代码receive.py：

{% highlight python %}
#!/usr/bin/env python
# coding=utf-8

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()
channel.queue_declare(queue='hello')

def callback(ch, method, properties, body):
    print "[x]Received %r" % body

channel.basic_consume(callback, queue='hello',no_ack=True)
print "[*]Waiting for messages.To exit press CTRL+C"
channel.start_consuming()
{% endhighlight %}

代码本身很简单，但是在运行的时候遇到了一些问题。直接运行send.py会报错。这是因为rabbitmq-server只允许guest用户在本地连接，若想通过其他计算机远程连接，就必须修改rabbitmq的配置文件。配置文件位于`/etc/rabbitmq/rabbitmq.config`。打开配置文件，找到下面这句：

```
 %% Uncomment the following line if you want to allow access to the
 %% guest user from anywhere on the network.
 %% {loopback_users, []},
```

注释中说明：**如果想要在网络中其他计算机访问guest用户，取消下面的注释**。我们将注释去掉之后，重启rabbitmq-server，重启失败！！擦泪～

后经反复尝试，多方打探，根据[官网](http://www.rabbitmq.com/access-control.html)所说:

```
 A complete rabbitmq.config which does this would look like:

[{rabbit, [{loopback_users, []}]}].
```

我们猜测：只有loopback_users一个配置项时，后面的','也应该去掉，果断去掉，配置文件改成：

```
 %% Uncomment the following line if you want to allow access to the
 %% guest user from anywhere on the network.
 {loopback_users, []}
```

再次重启rabbitmq-server，成功！既然成功了，我们尝试在agent端访问消息队列管理页面：http://server:15672，访问不到！！既然访问不到，那恐将也不能向server发消息。猜测可能是防火墙的问题。于是在server防火墙中开启5672和15672端口：

```
firewall-cmd --add-port=5672/tcp
firewall-cmd --add-port=15672/tcp
```

然后访问http://server:15672，成功！之后在agent执行`python send.py`，在server端执行`python receive.py`，你就可以看到'Hello World!'从agent流淌到server啦！