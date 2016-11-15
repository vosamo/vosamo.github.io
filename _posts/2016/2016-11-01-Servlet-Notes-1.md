---
layout: post
title: Servlet学习笔记之一
categories:
- 编程
tags:
- Java
---

## Servlet是什么？

Java Servlet是运行在Web服务器或应用服务器上的程序，它是作为来自Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层。

## Servlet架构

下图展示了Servlet在Web应用程序中的位置：

<center> ![servlet架构](/img/servlet-arch.jpg) </center>

## Servlet的任务

- 处理http请求，读取客户端发送的显式数据，比如表单数据
- 读取客户端发送的隐式数据，比如cookies、媒体类型和浏览器能理解的压缩格式等
- 处理数据并生成结果。这个过程可能需要访问数据库，执行 RMI 或 CORBA 调用，调用 Web 服务，或者直接计算得出对应的响应
- 发送显式的数据到客户端。该文档的格式可以是多种多样的，包括文本文件（HTML 或 XML）、二进制文件（GIF 图像）、Excel 等
- 发送隐式的 HTTP 响应到客户端。这包括告诉浏览器或其他客户端被返回的文档类型，设置 cookies 和缓存参数，以及其他类似的任务

在Eclipse中进行Servlet开发的话需要将servlet-api.jar包添加到build path中。

## Servlet的生命周期

Servlet的生命周期是从创建到销毁的整个过程。以下是Servlet遵循的过程：

1. Servlet通过调用init()方法进行初始化
2. Servlet通过调用service()方法来处理客户端的请求
3. Servlet通过调用destroy()方法来终止结束
4. 最后Servlet由JVM的垃圾回收器回收

### init()方法

init()方法仅在Servlet第一次创建的时候被调用一次，在Servlet的整个生命周期中仅调用这一次。它主要是完成一些初始化的工作，加载一些简单的数据。

init()方法的定义如下：

{% highlight java %}
public void init() throws ServletException {
  // 初始化代码...
}
{% endhighlight %}

### service()方法

service()方法是执行实际任务的方法，也就是响应http请求的方法。Servlet对象时单例，但访问是多线程。

每次服务器接收到一个Servlet请求时，服务器会产生一个新的线程并调用服务。service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 doGet、doPost、doPut，doDelete 等方法。

{% highlight java %}
public void init() throws ServletException {
  // 初始化代码...
}
{% endhighlight %}

service() 方法由容器调用，service 方法在适当的时候调用 doGet、doPost、doPut、doDelete 等方法。doGet和doPost是最常用的两个方法。

### doGet()方法

客户端发起的GET请求交由该方法处理。

{% highlight java %}
public void doGet(HttpServletRequest request,
                  HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}
{% endhighlight %}


### doGet()方法

客户端发起的POST请求交由该方法处理。

{% highlight java %}
public void doPost(HttpServletRequest request,
                   HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}
{% endhighlight %}

### destroy()方法

destroy()方法和init()方法一样也是只调用一次，在Servlet生命周期结束时被调用。destroy() 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。

在调用 destroy() 方法之后，servlet 对象被标记为垃圾回收。

{% highlight java %}
public void destroy() {
  // 终止化代码...
}
{% endhighlight %}

### 架构图

下图展示了一个典型的Servlet生命周期：

<center> ![Servlet生命周期](/img/Servlet-LifeCycle.jpg) </center>

- 第一个到达服务器的HTTP请求被委派到Servlet容器
- Servlet容器在调用service()方法之前加载 Servlet
- 然后Servlet容器处理由多个线程产生的多个请求，每个线程执行一个单一的 Servlet 实例的service()方法
